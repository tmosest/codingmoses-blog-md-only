---
title: LeetCode 1814. Count Nice Pairs in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1814. Count Nice Pairs in an Array – A Complete, Interview‑Ready Solution  
*(Java | Python | C++)*  

---

## TL;DR  
- **Problem** – Count the number of index pairs *(i, j)* with `i < j` such that  
  `nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])`.  
- **Key insight** – The equation is equivalent to `nums[i] – rev(nums[i]) == nums[j] – rev(nums[j])`.  
- **Solution** – Compute the value `d = nums[k] – rev(nums[k])` for each element, store the frequency of each `d` in a hash map, and count `freq[d] choose 2` pairs.  
- **Complexity** – `O(n)` time, `O(n)` extra space (where `n = nums.length`).  
- **Modulo** – All answers are taken modulo `1 000 000 007`.  

---

## 1. Problem Recap (From LeetCode)

> You are given an array `nums` of non‑negative integers.  
> `rev(x)` is the reverse of the integer `x`.  
> A pair `(i, j)` is *nice* if `i < j` and  
> `nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])`.  
> Return the number of nice pairs modulo `1 000 000 007`.

---

## 2. Why the “difference” trick works

```
nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])
⇔ nums[i] - rev(nums[i]) == nums[j] - rev(nums[j])
```

*Proof* – Move the terms containing the same index to the same side:

```
nums[i] - rev(nums[i]) = nums[j] - rev(nums[j])
```

Thus two indices form a nice pair **iff** their *differences* are equal.  
So the problem collapses to: *count how many equal “difference” values exist.*

---

## 3. Algorithm

1. **Reverse helper** – convert an integer to its reversed form.  
2. For each element `x` in `nums`:
   * `d = x - rev(x)` (store in a `long`/`long long` to avoid overflow).  
   * Increment the counter of `d` in a hash map.
3. After the scan, for each distinct `d` with frequency `c` add  
   `c * (c – 1) / 2` to the answer (modulo `MOD`).
4. Return the answer.

---

## 4. Code

### 4.1 Java (8+)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countNicePairs(int[] nums) {
        Map<Long, Long> freq = new HashMap<>();

        for (int x : nums) {
            long d = x - reverse(x);
            freq.merge(d, 1L, Long::sum);
        }

        long ans = 0;
        for (long c : freq.values()) {
            if (c > 1) {
                ans = (ans + (c * (c - 1) / 2) % MOD) % MOD;
            }
        }
        return (int) ans;
    }

    private long reverse(int num) {
        long rev = 0;
        while (num > 0) {
            rev = rev * 10 + num % 10;
            num /= 10;
        }
        return rev;
    }
}
```

### 4.2 Python 3

```python
class Solution:
    MOD = 1_000_000_007

    def countNicePairs(self, nums: List[int]) -> int:
        freq = {}
        for x in nums:
            d = x - self._rev(x)
            freq[d] = freq.get(d, 0) + 1

        ans = 0
        for c in freq.values():
            ans = (ans + c * (c - 1) // 2) % self.MOD
        return ans

    def _rev(self, num: int) -> int:
        rev = 0
        while num:
            rev = rev * 10 + num % 10
            num //= 10
        return rev
```

### 4.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countNicePairs(vector<int>& nums) {
        const long long MOD = 1'000'000'007LL;
        unordered_map<long long, long long> freq;

        for (int x : nums) {
            long long d = (long long)x - reverseNum(x);
            ++freq[d];
        }

        long long ans = 0;
        for (auto &p : freq) {
            long long c = p.second;
            if (c > 1)
                ans = (ans + (c * (c - 1) / 2) % MOD) % MOD;
        }
        return (int)ans;
    }

private:
    long long reverseNum(int n) {
        long long rev = 0;
        while (n) {
            rev = rev * 10 + n % 10;
            n /= 10;
        }
        return rev;
    }
};
```

---

## 5. Blog‑Style Article: *The Good, the Bad, and the Ugly of Counting Nice Pairs*

> **Meta‑description (SEO)** – Want to land that coding interview?  
> Master LeetCode 1814 “Count Nice Pairs” in Java, Python, and C++!  
> Learn the hash‑map trick, pitfalls, and real‑world interview tips.

---

### 5.1 The Good – What Makes This Problem a Gold‑Mine

1. **Simplicity of the math** – Once you notice the cancellation trick (`x - rev(x)`), the whole problem collapses to a frequency count.  
2. **High performance** – The O(n) solution works comfortably for the maximum constraints (`10^5` numbers).  
3. **Language‑agnostic** – The same idea fits Java, Python, C++, Go, etc., so you can answer in whichever language the interviewer prefers.  
4. **Great interview showcase** – Demonstrates:
   - Ability to spot algebraic simplification.
   - Proper use of hash maps for counting.
   - Handling large numbers with modular arithmetic.

---

### 5.2 The Bad – Common Pitfalls and How to Avoid Them

| Pitfall | Why it matters | Fix |
|---------|----------------|-----|
| **Using `int` for reversed values** | Reversing 1 000 000 000 produces 1, which is fine, but intermediate multiplications (e.g., `rev * 10`) can overflow `int`. | Use `long`/`long long` for the reverse helper and difference calculations. |
| **Neglecting the modulo** | Even though the answer is capped, intermediate sums like `c * (c-1) / 2` can overflow 64‑bit if `c` is huge (theoretically up to 10^5). | Compute modulo after each addition and cast to `long long`. |
| **Not handling zeros** | `rev(0)` should be `0`, but the loop `while (num)` will skip, so you must handle `num == 0` explicitly. | Either initialize `rev = 0` and use `while (num > 0)` (which works), or special‑case zero. |
| **Using a plain array for frequencies** | The difference value can be negative, so an array indexed by `d` is not feasible. | Use a hash map/dictionary keyed by `d`. |
| **Off‑by‑one errors** | Remember that pairs are *unordered*: `(i, j)` and `(j, i)` are the same. The `c * (c-1)/2` formula accounts for this. | Verify with small examples. |

---

### 5.3 The Ugly – Edge‑Case Scenarios and “Tricky” Tests

| Test | Expected Answer | Why it’s tricky |
|------|------------------|-----------------|
| `nums = [0, 0, 0]` | 3 | All differences are zero; you must count 3 pairs. |
| `nums = [100, 1]` | 0 | `rev(100) = 1`, `rev(1) = 1`; differences: `99` and `0` → no match. |
| `nums = [123456789, 987654321]` | 1 | Both have different differences but `rev` flips the order. |
| Very large values (close to `10^9`) | Works | Ensure reverse does not overflow. |
| Randomly shuffled long array | Pass | Performance should still be linear. |

---

### 5.4 Interview‑Friendly Talk‑Track

1. **Explain the observation**: `nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])` → `nums[i] - rev(nums[i]) == nums[j] - rev(nums[j])`.  
2. **State the approach**: compute the “difference” for each number, use a hash map to count identical differences, then use the combinatorial formula.  
3. **Highlight complexity**: O(n) time, O(n) space, constant factor tiny.  
4. **Mention edge‑case handling**: 64‑bit arithmetic, modulo, zeros.  
5. **Show code**: A concise implementation in the language of choice.  
6. **Optional**: Discuss why we use `long` for `rev` and `difference`.  

---

### 5.5 Take‑away

*The beauty of “Count Nice Pairs” lies in turning a seemingly complex equality into a simple counting problem. By mastering this trick you not only solve LeetCode 1814 efficiently but also demonstrate a powerful mindset that interviewers love: see the underlying pattern, reduce to basics, and implement cleanly.*

---

## 6. Final Checklist Before Your Next Interview

- [ ] **Explain the math simplification** clearly.
- [ ] **Write a reverse helper** that works for 0 and avoids overflow.
- [ ] **Use a hash map** (`Map<Long, Long>` in Java, `unordered_map<long long, long long>` in C++, `dict` in Python).
- [ ] **Count pairs** with `c * (c-1) / 2` and apply modulo `1 000 000 007`.
- [ ] **Test with edge cases** (all zeros, large numbers, random data).
- [ ] **Practice the talk‑track** so you can answer confidently in 2–3 minutes.

Happy coding, and good luck with that interview! 🚀