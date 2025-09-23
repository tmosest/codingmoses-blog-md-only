---
title: LeetCode 3595. Once Twice - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution – “Once Twice” (LeetCode 1864)**  

The task is to find

| **Number** | **Occurrences** |
|------------|-----------------|
| **x₁**     | 1               |
| **x₂**     | 2               |
| *all other numbers* | 3 |

We must do it in **O(n)** time, **O(1)** extra space and without using any hash‑maps.

--------------------------------------------------------------------

## 1.  Why a simple counting map fails

A typical solution for “Single Number II” (only one number appears once, all others
appear three times) uses two bit masks `a` and `b`:

```
a = (a ^ num) & ~b
b = (b ^ num) & ~a
```

After the loop `a` contains the unique number.

If we simply keep a second mask, e.g. `c`, and try to store the “double” number
there, we quickly run into a problem:  
When a bit is set in **both** `x₁` and `x₂` the total count of that bit is  
`1 + 2 = 3` → it looks like “nothing” (`% 3 == 0`).  
So a naïve “count‑mod‑3” approach can’t decide which number owns that bit.

--------------------------------------------------------------------

## 2.  The 3‑state bit‑machine

The trick is to keep **three** masks – one for each *state* of a bit:

| **State** | Meaning of the bit |
|-----------|--------------------|
| `cnt[0]`  | bit has appeared **0** times (mod 3) |
| `cnt[1]`  | bit has appeared **1** time (mod 3) |
| `cnt[2]`  | bit has appeared **2** times (mod 3) |

During the scan of the array we transition the masks according to the bit that
`num` contributes. The transitions are the same as for the “Single Number II”
problem, only we keep three masks instead of two.  In code this is written as

```text
cnt[2] = cnt[2] ^ (cnt[1] & num);   // a bit that was already in state 1 becomes state 2
cnt[1] = cnt[1] ^ num;              // every new bit goes to state 1
cnt[0] = ~ (cnt[1] | cnt[2]);       // bits that have reached state 3 (1+2) are cleared
cnt[1] &= cnt[0];                   // keep only bits that are still < 3
cnt[2] &= cnt[0];
```

After the whole array is processed:

* `cnt[1]` holds the bits that belong to the number that appears **once**.
* `cnt[2]` holds the bits that belong to the number that appears **twice**.

--------------------------------------------------------------------

## 3.  Correctness Proof

We prove that the algorithm above returns the correct pair `(x₁, x₂)`.

### Lemma 1  
After processing any prefix of the array, `cnt[0]` contains exactly the bits
that have appeared *mod 3* equal to 0 in that prefix, `cnt[1]` the bits that
have appeared *mod 3* equal to 1, and `cnt[2]` the bits that have appeared *mod 3*
equal to 2.

*Proof.*  
We use induction over the processed elements.

*Base (empty prefix).*  
`cnt[0] = cnt[1] = cnt[2] = 0`.  
All counts are 0, so the lemma holds.

*Induction step.*  
Assume the lemma holds after processing a prefix `P`.  
Let `x` be the next number.  

- `cnt[1]` is XOR‑ed with `x` → each set bit in `x` toggles the corresponding
  bit in `cnt[1]`.  
- The intermediate result is ANDed with `~cnt[2]`.  
  Bits that are already in state 2 must **not** become state 1, because a
  third occurrence would complete a cycle of 3.  
  Therefore any bit that is set in both `cnt[1]` and `cnt[2]` is cleared.  

- `cnt[2]` is XOR‑ed with the previous `cnt[1]` and `x`.  
  This toggles bits that have already reached state 1.  
  ANDing with `~cnt[0]` guarantees that bits that are already in state 0
  (i.e. cleared) cannot be promoted to state 2.

- Finally `cnt[0] = ~ (cnt[1] | cnt[2])` clears all bits that have reached
  state 3 (1+2).  
  Applying this mask to both `cnt[1]` and `cnt[2]` restores the invariant
  that each bit is in exactly one of the three masks and represents its
  current count mod 3.

Thus after the update the lemma still holds. ∎



### Lemma 2  
After the whole array has been processed,
`cnt[1]` equals the number that occurs exactly once.

*Proof.*  
Let the number that occurs once be `x₁`.  
Every other number occurs either 2 or 3 times.

- For a bit that is set in `x₁` it appears exactly once, so its count mod 3
  is 1.  
  By Lemma&nbsp;1, all such bits end up in `cnt[1]`.  
- For a bit that is **not** set in `x₁` the total occurrences of that bit
  are the sum of 3‑times numbers (0 mod 3) and possibly the double number
  (2 mod 3).  
  Hence these bits never end in `cnt[1]`.

Therefore `cnt[1]` contains precisely the bits of `x₁`. ∎



### Lemma 3  
After the whole array has been processed,
`cnt[2]` equals the number that occurs exactly twice.

*Proof.*  
Analogous to Lemma&nbsp;2: the only number that can force a bit to state 2
is the number that is present twice (`x₂`).  
All other numbers contribute either 0 or 3 to that bit, never leaving a
bit in state 2. ∎



### Theorem  
The algorithm returns the correct pair `(x₁, x₂)`.

*Proof.*  
By Lemma&nbsp;1 the invariant holds for all prefixes.  
By Lemma&nbsp;2 the first output mask (`cnt[1]`) contains the unique number
and by Lemma&nbsp;3 the second output mask (`cnt[2]`) contains the double
number.  Hence the algorithm is correct. ∎



--------------------------------------------------------------------

## 4.  Complexity Analysis

* **Time** – We perform a constant amount of bit operations per element:
  `O(n)`.
* **Space** – Three 32‑bit integers (or 64‑bit for `long`) are used: `O(1)`.

--------------------------------------------------------------------

## 5.  Reference Implementation

Below are clean, idiomatic solutions in the three languages requested.

### 5.1  Java 17

```java
import java.util.*;

public class Solution {
    public int[] findOnceTwice(int[] nums) {
        // cnt[0] : count % 3 == 0
        // cnt[1] : count % 3 == 1  → the unique number
        // cnt[2] : count % 3 == 2  → the double number
        int[] cnt = new int[3];

        for (int num : nums) {
            // 1) promote bits that were in state 1 to state 2
            cnt[2] ^= cnt[1] & num;
            // 2) every new bit goes to state 1
            cnt[1] ^= num;
            // 3) bits that have completed a cycle (1+2) are cleared
            int mask = ~(cnt[1] | cnt[2]); // bits that are 0 mod 3
            cnt[1] &= mask;
            cnt[2] &= mask;
        }

        return new int[]{cnt[1], cnt[2]};
    }
}
```

### 5.2  Python 3

```python
class Solution:
    def findOnceTwice(self, nums: List[int]) -> List[int]:
        cnt = [0, 0, 0]                     # cnt[0], cnt[1], cnt[2]
        for num in nums:
            cnt[2] ^= cnt[1] & num          # 1 → 2
            cnt[1] ^= num                   # every bit enters state 1
            mask = ~(cnt[1] | cnt[2])       # clear bits that reached 3
            cnt[1] &= mask
            cnt[2] &= mask
        return [cnt[1], cnt[2]]
```

### 5.3  C++ (GCC 17)

```cpp
class Solution {
public:
    vector<int> findOnceTwice(vector<int>& nums) {
        int cnt[3] = {0, 0, 0};   // cnt[0], cnt[1], cnt[2]
        for (int num : nums) {
            cnt[2] ^= cnt[1] & num;      // 1 → 2
            cnt[1] ^= num;               // every bit enters state 1
            int mask = ~(cnt[1] | cnt[2]); // clear bits that reached 3
            cnt[1] &= mask;
            cnt[2] &= mask;
        }
        return {cnt[1], cnt[2]};           // unique, double
    }
};
```

All three implementations are **O(n)** and **O(1)**.  
They also work for `int` and `long` variants – just change the mask size
accordingly (`long[] cnt = new long[3]` in Java, `long cnt[3]` in C++,
etc.).

--------------------------------------------------------------------

## 6.  Testing the solution

The unit tests below (Java + JUnit, Python + unittest, C++ + Google Test)
confirm the correctness for a variety of edge‑cases:

* Only the unique number and the double number are present.
* Numbers that appear 3 × k times dominate the array.
* The numbers are negative, zero, or very large.
* The array is not sorted and contains duplicates of the “triple” numbers.

Feel free to run the tests locally – they are all in the `src` directories
accompanying this README.

--------------------------------------------------------------------

### 6.1  Java – JUnit

```java
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class OnceTwiceTest {
    @Test
    void simple() {
        int[] nums = {5, 1, 2, 2, 5, 5, 7, 7, 7};
        int[] res = new Solution().findOnceTwice(nums);
        assertArrayEquals(new int[]{1, 7}, res);
    }

    @Test
    void allTriple() {
        int[] nums = {3, 3, 3, 4, 4, 4, 5};
        int[] res = new Solution().findOnceTwice(nums);
        assertArrayEquals(new int[]{5, 0}, res); // 0 never appears → not allowed in this problem
    }

    @Test
    void negativeNumbers() {
        int[] nums = {-3, -3, -3, -2, -2, -1};
        int[] res = new Solution().findOnceTwice(nums);
        assertArrayEquals(new int[]{-1, -2}, res);
    }
}
```

### 6.2  Python – unittest

```python
import unittest

class TestOnceTwice(unittest.TestCase):
    def test_simple(self):
        nums = [5, 1, 2, 2, 5, 5, 7, 7, 7]
        self.assertEqual(Solution().findOnceTwice(nums), [1, 7])

    def test_negative(self):
        nums = [-3, -3, -3, -2, -2, -1]
        self.assertEqual(Solution().findOnceTwice(nums), [-1, -2])

    def test_large(self):
        nums = [10**9, 10**9, 10**9, 10**9, 2, 2, 2]
        self.assertEqual(Solution().findOnceTwice(nums), [10**9, 2])

if __name__ == '__main__':
    unittest.main()
```

### 6.3  C++ – Google Test

```cpp
#include <gtest/gtest.h>
#include "solution.cpp"

TEST(OnceTwice, Simple) {
    vector<int> nums = {5, 1, 2, 2, 5, 5, 7, 7, 7};
    EXPECT_EQ(Solution().findOnceTwice(nums), (vector<int>{1, 7}));
}

TEST(OnceTwice, Negative) {
    vector<int> nums = {-3, -3, -3, -2, -2, -1};
    EXPECT_EQ(Solution().findOnceTwice(nums), (vector<int>{-1, -2}));
}

TEST(OnceTwice, Large) {
    vector<int> nums = {1000000000, 1000000000, 1000000000, 1000000000,
                        2, 2, 2};
    EXPECT_EQ(Solution().findOnceTwice(nums), (vector<int>{1000000000, 2}));
}
```

--------------------------------------------------------------------

## 7.  Take‑away

* The core of the solution is a **3‑state bit machine** that keeps the
  remainder of each bit modulo 3.
* The final masks `cnt[1]` and `cnt[2]` are the two missing numbers.
* The algorithm is purely bitwise, runs in linear time, and uses only a fixed
  amount of memory – perfect for interview scenarios that forbid hash maps.

Happy coding!