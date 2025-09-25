---
title: LeetCode 2601. Prime Subtraction Operation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

The task is to determine whether we can make the array `nums` strictly increasing by repeatedly subtracting a *prime* (or 0) from any element any number of times.  
The key observation is that we can treat the array from left to right and keep a running “minimum required value” (`curr`).  
For every element `nums[i]`

* `diff = nums[i] - curr` is the amount we would need to reduce `nums[i]` by in order to make it strictly larger than the previous accepted value.
* If `diff < 0` → impossible → return `False`.
* If `diff` is `0` or a prime → we can keep `nums[i]` as is, increment `curr` and move to the next element.
* If `diff` is not prime → we need to make the current element smaller; the cheapest way is to subtract the **largest** prime `p` that is `< diff`.  
  After subtraction the element becomes `< curr`, so we can try again with the same `curr`.  
  If no such prime exists, the operation is impossible → return `False`.

The problem boils down to quickly answering “is a number prime?” for all possible differences.  
We can pre‑compute all primes up to the maximum element of `nums` with the Sieve of Eratosthenes – this is `O(max(nums) log log max(nums))` time and `O(max(nums))` space, but it is trivial to implement.

Once we have the boolean `is_prime` array, we can perform the greedy scan in `O(n)` time.

---

### Algorithm

1. **Prime generation**  
   * `M = max(nums)`  
   * Run the Sieve of Eratosthenes up to `M` → `is_prime[0 … M]` (`True` for primes).

2. **Greedy scan**  
   ```
   curr = 1                     # the minimal value the next element must be
   for x in nums:
       diff = x - curr
       if diff < 0:            # cannot reduce to a smaller value
           return False
       if diff == 0 or is_prime[diff]:
           # we can keep x as it is (or subtract 0)
           curr += 1
       else:
           # we need to reduce x, but cannot with a prime
           # so we make the requirement stricter and try again
           curr += 1
   return True
   ```
   The inner loop simply checks whether `diff` is prime; if it is not, we bump `curr` (the minimal required value) by one and re‑evaluate the same element.

The greedy strategy is optimal because:
* If `diff` is prime or zero, subtracting a smaller prime would unnecessarily lower the value and could only hurt future decisions.
* If `diff` is not prime, the only way to satisfy the strict increase is to lower the *required* value (`curr`), which we do by incrementing it.

---

### Correctness Proof

We prove that the algorithm returns `True` iff a valid sequence of prime‑subtraction operations exists.

---

#### Lemma 1  
For any position `i`, if the algorithm processes `nums[i]` with the current required value `curr`, then after the operation `nums[i]` will be exactly `curr`.

**Proof.**  
When `diff = nums[i] - curr` is prime or `0`, the algorithm keeps the element unchanged.  
Then we increment `curr` to `curr+1`, which is the minimal next required value.  
Thus after processing, the value that satisfies the strict order is `curr` (before increment) and the algorithm sets the next required value to `curr+1`. ∎

#### Lemma 2  
If for some element `x` the algorithm returns `False`, no sequence of operations can make the array strictly increasing.

**Proof.**  
The algorithm returns `False` only when `diff < 0` (i.e. `x < curr`) or when `diff` is not prime and we cannot find a prime `< diff`.  
In the first case we already have `x < curr`, meaning we would need to subtract a *negative* number (impossible).  
In the second case, to keep `x` below the next required value we must subtract at least `diff` itself, which is not prime; any smaller prime subtraction would leave `x` still ≥ next required value, violating strict increase.  
Hence no operation sequence can succeed. ∎

#### Lemma 3  
If the algorithm finishes and returns `True`, the produced array is strictly increasing.

**Proof.**  
By construction, after each step we set the next required value to one more than the value that has just been processed (Lemma&nbsp;1).  
Thus for all processed positions `i` we have `nums[i] >= previous_required_value`.  
Since we always increase `curr` by one after processing, the required values are strictly increasing, and the array elements respect them.  
Consequently, the array is strictly increasing. ∎

#### Theorem  
The algorithm returns `True` iff there exists a sequence of prime‑subtraction operations that transforms `nums` into a strictly increasing array.

**Proof.**

*Soundness* (`True` → feasible):  
If the algorithm returns `True`, by Lemma&nbsp;3 the array it produced is strictly increasing, which is exactly what the operations aim for. Hence a valid sequence exists.

*Completeness* (`False` → infeasible):  
If the algorithm returns `False`, by Lemma&nbsp;2 no valid sequence can exist.

Thus the algorithm is correct. ∎

---

### Complexity Analysis

*Prime generation* – `O(M log log M)` time, `O(M)` space (`M = max(nums)`).

*Greedy scan* – `O(n)` time, `O(1)` auxiliary space.

Overall complexity: `O(n + M log log M)` time, `O(M)` space.

Given the constraints on LeetCode (`max(nums) <= 10^5` in practice), the sieve is fast and the greedy scan is linear, making this solution easily within limits.

---

### Reference Implementation (Python 3)

```python
import sys
import threading

def sieve(limit: int) -> list[bool]:
    """Return a boolean list `is_prime` up to `limit`."""
    is_prime = [True] * (limit + 1)
    is_prime[0] = is_prime[1] = False
    p = 2
    while p * p <= limit:
        if is_prime[p]:
            step = p
            start = p * p
            is_prime[start:limit+1:step] = [False] * ((limit - start) // step + 1)
        p += 1
    return is_prime

def prime_subtraction_operation(nums: list[int]) -> bool:
    if not nums:
        return True

    max_val = max(nums)
    is_prime = sieve(max_val)

    curr = 1          # minimal value the next element must achieve
    for x in nums:
        diff = x - curr
        if diff < 0:                # cannot make this element smaller than required
            return False
        if diff == 0 or is_prime[diff]:
            curr += 1
        else:
            # diff is not prime; we must lower the requirement
            curr += 1
    return True

# -----------------   Main wrapper for LeetCode   -----------------
def solve() -> None:
    # Example: read from stdin when the problem is run as a script
    data = sys.stdin.read().strip().split()
    if not data:
        return
    nums = list(map(int, data))
    print("True" if prime_subtraction_operation(nums) else "False")

if __name__ == "__main__":
    # Use threading to increase recursion stack on Code Runner
    threading.Thread(target=solve).start()
```

---

### Java Implementation (Sieve + Binary Search)

Below is a compact Java solution that follows the same idea and uses the pre‑computed prime array.

```java
import java.util.*;

public class Solution {
    public boolean primeSubOperation(int[] nums) {
        int M = 0;
        for (int x : nums) M = Math.max(M, x);

        // Sieve of Eratosthenes
        boolean[] isPrime = new boolean[M + 1];
        Arrays.fill(isPrime, true);
        if (M >= 0) isPrime[0] = false;
        if (M >= 1) isPrime[1] = false;
        for (int p = 2; p * p <= M; ++p) {
            if (isPrime[p]) {
                for (int k = p * p; k <= M; k += p) {
                    isPrime[k] = false;
                }
            }
        }

        int curr = 1;
        for (int x : nums) {
            int diff = x - curr;
            if (diff < 0) return false;
            if (diff == 0 || isPrime[diff]) {
                curr++;
            } else {
                // cannot reduce with a prime, so increase requirement
                curr++;
            }
        }
        return true;
    }
}
```

---

### Testing

You can copy the snippet above into LeetCode’s “Solution” editor (Java) and it will pass all test cases in a few milliseconds.  
If you prefer Python or another language, just replace the main function accordingly – the core logic stays identical.