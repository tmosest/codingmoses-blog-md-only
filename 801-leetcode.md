---
title: LeetCode 801. Minimum Swaps To Make Sequences Increasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem 801 – Minimum Number of Swaps to Make Sequences Increasing**

Given two equal‑length arrays `nums1` and `nums2`, we may swap the i‑th elements of the two arrays any number of times.  
After all swaps we want **both** sequences to be strictly increasing.  
Return the minimum number of swaps that must be performed.

--------------------------------------------------------------------

#### 1.  Observation – only two states per position

For every index `i` we have only two possibilities

| state | meaning |
|-------|---------|
| `notSwap[i]` | we did **not** swap at position `i` |
| `swap[i]` | we **did** swap at position `i` |

`notSwap[i]` and `swap[i]` store the minimal number of swaps needed for the sub‑array
`[0 … i]` **with** the above state at index `i`.

The answer for the whole array is `min( notSwap[n‑1] , swap[n‑1] )`.

--------------------------------------------------------------------

#### 2.  Transition

Assume we are at position `i` (`i>0`).

Let

```
A = nums1[i-1]   B = nums1[i]
C = nums2[i-1]   D = nums2[i]
```

1. **Keep the current pair unchanged**

   This is possible only if both sequences are already increasing:

   ```
   if (A < B && C < D) {
       notSwap[i] = notSwap[i-1]          // previous state stays the same
       swap[i]    = swap[i-1] + 1         // we need one more swap at i
   }
   ```

2. **Swap the current pair**

   After swapping we must have

   ```
   if (A < D && C < B) {
       // the previous state is opposite to the current one
       notSwap[i] = min(notSwap[i],  swap[i-1])   // previous was swapped
       swap[i]    = min(swap[i],      notSwap[i-1] + 1) // previous was not swapped
   }
   ```

   Notice we *minimize* with the opposite previous state because
   after swapping the order of the two indices reverses.

--------------------------------------------------------------------

#### 3.  Implementation – bottom‑up with O(1) space

Only the values of the previous index are required, therefore we keep two
variables instead of an entire array.

```java
class Solution {
    public int minSwap(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int noSwap = 0;   // swaps needed if we don't swap at the current index
        int swap   = 1;   // swaps needed if we do swap at the current index

        for (int i = 1; i < n; i++) {
            int newNoSwap = Integer.MAX_VALUE;
            int newSwap   = Integer.MAX_VALUE;

            // case 1: keep the pair as it is
            if (nums1[i-1] < nums1[i] && nums2[i-1] < nums2[i]) {
                newNoSwap = noSwap;           // previous state unchanged
                newSwap   = swap + 1;          // previous state swapped + current swap
            }

            // case 2: swap the current pair
            if (nums1[i-1] < nums2[i] && nums2[i-1] < nums1[i]) {
                newNoSwap = Math.min(newNoSwap, swap);   // previous state swapped
                newSwap   = Math.min(newSwap, noSwap + 1); // previous state not swapped
            }

            noSwap = newNoSwap;
            swap   = newSwap;
        }

        return Math.min(noSwap, swap);
    }
}
```

--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm returns the minimum number of swaps.

---

##### Lemma 1  
For every index `i` the values `noSwap` and `swap` computed by the algorithm are the minimal numbers of swaps needed to make the prefix `[0 … i]` strictly increasing **respectively** when the i‑th pair is **not swapped** or **swapped**.

**Proof.**

We use induction on `i`.

*Base (`i = 0`)*:  
`noSwap = 0` – zero swaps when we keep the first pair.  
`swap   = 1` – one swap when we swap the first pair.  
Both are clearly minimal.

*Induction step:*  
Assume the lemma holds for `i-1`.  
When we are at index `i` we consider two possibilities:

1. **Keep the i‑th pair unchanged**  
   It is valid iff the two sequences are increasing without swap at this
   index.  
   In this case the minimal number of swaps for the whole prefix is
   exactly the minimal number for the prefix `[0 … i‑1]` with the same
   state (`noSwap` or `swap`).  
   The algorithm sets `newNoSwap = noSwap` and `newSwap = swap + 1`
   accordingly, which are optimal by induction hypothesis.

2. **Swap the i‑th pair**  
   It is valid iff after the swap the two sequences remain increasing.
   This means that the state at index `i` is opposite to the state at
   index `i‑1`.  
   Hence we must take the minimal number of swaps for the previous
   prefix with the opposite state.  
   The algorithm updates `newNoSwap` with `swap` and
   `newSwap` with `noSwap + 1`.  
   Again by induction hypothesis these are optimal.

No other choice of state is possible, so the algorithm chooses the
minimum among all valid possibilities.  
Thus `newNoSwap` and `newSwap` are the optimal values for index `i`. ∎



##### Lemma 2  
After processing all indices the variables `noSwap` and `swap` contain the minimal number of swaps for the entire arrays
when the last pair is **not swapped** or **swapped** respectively.

**Proof.**

Immediate from Lemma&nbsp;1 applied to `i = n‑1`. ∎



##### Theorem  
`min(minSwap(nums1, nums2))` returned by the algorithm equals the minimal
number of swaps required to make both sequences strictly increasing.

**Proof.**

By Lemma&nbsp;2 we have the optimal counts for both final states.
The required minimal number of swaps is the smaller of these two
values.  
The algorithm returns exactly `Math.min(noSwap, swap)`. ∎



--------------------------------------------------------------------

#### 5.  Complexity Analysis

```
Time   : O(n)   (single pass over the arrays)
Space  : O(1)   (constant number of integer variables)
```

--------------------------------------------------------------------

#### 6.  Reference Implementation (Java 17)

```java
import java.util.*;

public class Solution {
    public int minSwap(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int noSwap = 0;   // minimal swaps if i‑th pair is not swapped
        int swap   = 1;   // minimal swaps if i‑th pair is swapped

        for (int i = 1; i < n; i++) {
            int newNoSwap = Integer.MAX_VALUE;
            int newSwap   = Integer.MAX_VALUE;

            // keep current pair
            if (nums1[i-1] < nums1[i] && nums2[i-1] < nums2[i]) {
                newNoSwap = noSwap;          // same state as before
                newSwap   = swap + 1;         // previous swap + current swap
            }

            // swap current pair
            if (nums1[i-1] < nums2[i] && nums2[i-1] < nums1[i]) {
                newNoSwap = Math.min(newNoSwap, swap);    // previous swapped
                newSwap   = Math.min(newSwap, noSwap + 1); // previous not swapped
            }

            noSwap = newNoSwap;
            swap   = newSwap;
        }

        return Math.min(noSwap, swap);
    }
}
```

The code follows exactly the algorithm proven correct above and
runs in `O(n)` time with constant additional memory.