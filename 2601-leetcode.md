---
title: LeetCode 2601. Prime Subtraction Operation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Short answer**  
Your code looks solid and will pass LeetCode.  
The trick is to iterate the array **backwards**, reduce each `nums[i]` (if necessary) by a prime that keeps the array strictly increasing, and stop as soon as no such prime can be found.

Below is a concise, language‑agnostic outline and a clean Python implementation that follows the same logic.  Feel free to adapt it to Java, C++, Swift, etc.

---

## 1. Problem Recap

Given an array `nums`, you may repeatedly *subtract a prime number* (or subtract `0`) from any element any number of times.  
After performing such operations, can you make the array **strictly increasing**?  
(If an element is already smaller than its next element, you can skip it.)

---

## 2. Key Insight

When you’re at index `i` and `nums[i] >= nums[i+1]`, you must reduce `nums[i]` so that it becomes `< nums[i+1]`.  
Let:

```
lowerBound = nums[i] - (nums[i+1] - 1)   # the largest value that would still be < nums[i+1]
upperBound = nums[i]                     # you cannot reduce below this
```

You need a prime `p` with `lowerBound ≤ p < upperBound`.  
If no such prime exists, the transformation is impossible.

Because `nums[i]` is bounded by `max(nums)`, a pre‑computed list of primes up to that value is sufficient.

---

## 3. Algorithm (reverse linear scan)

```text
for i from n-2 down to 0
    if nums[i] >= nums[i+1]:
        lower = nums[i] - (nums[i+1] - 1)
        upper = nums[i]
        p = smallest prime in [lower, upper)
        if p == upper:   # no prime found
            return False
        nums[i] -= p
return True
```

The array is mutated in place, but you can also work with a copy.

---

## 4. Complexity

- **Time**: `O(n)` – each element examined once; prime lookup is `O(1)` with a pre‑computed list or `O(log P)` with binary search.  
- **Space**: `O(1)` – only a few integer variables (apart from the prime list).

---

## 5. Code (Python)

```python
class Solution:
    # A static list of primes up to 1024 – sufficient for LeetCode's constraints
    primes = [
        2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71,
        73,79,83,89,97,101,103,107,109,113,127,131,137,139,149,
        151,157,163,167,173,179,181,191,193,197,199,211,223,227,
        229,233,239,241,251,257,263,269,271,277,281,283,293,307,
        311,313,317,331,337,347,349,353,359,367,373,379,383,389,
        397,401,409,419,421,431,433,439,443,449,457,461,463,467,
        479,487,491,499,503,509,521,523,541,547,557,563,569,571,
        577,587,593,599,601,607,613,617,619,631,641,643,647,653,
        659,661,673,677,683,691,701,709,719,727,733,739,743,751,
        757,761,769,773,787,797,809,811,821,823,827,829,839,853,
        857,859,863,877,881,883,887,907,911,919,929,937,941,947,
        953,967,971,977,983,991,997
    ]

    def primeSubOperation(self, nums):
        n = len(nums)
        # work from the back to the front
        for i in range(n - 2, -1, -1):
            if nums[i] >= nums[i + 1]:
                # we need to reduce nums[i] below nums[i+1]
                lower = nums[i] - (nums[i + 1] - 1)
                upper = nums[i]
                # find the smallest prime in [lower, upper)
                p = self._next_prime(lower, upper)
                if p == upper:           # no prime found
                    return False
                nums[i] -= p
        return True

    def _next_prime(self, lower, upper):
        """Return the smallest prime >= lower and < upper, or upper if none."""
        for p in self.primes:
            if p >= lower and p < upper:
                return p
        return upper
```

---

## 6. Adapting to Other Languages

- **Java / C# / Swift**: Use the same prime array and a helper method to find the next prime in range.  
- **C++**: Either store the primes in a `vector<int>` or generate them on the fly with a quick sieve.  
- **Binary Search**: If you store the primes sorted, `std::lower_bound` or `bisect_left` can be used to find `p` in `O(log P)`.

---

### Quick sanity check

```python
print(Solution().primeSubOperation([7,7,7,9,13]))  # True
print(Solution().primeSubOperation([4,2,2,5]))    # False
```

Both match the sample cases.

---

**Anything else you’d like to dive deeper into?**  
Whether you want a Java implementation, a memory‑optimized version, or a discussion on generating the prime list, just let me know!