---
title: LeetCode 1558. Minimum Numbers of Function Calls to Make Target Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1558 – Minimum Numbers of Function Calls to Make Target Array  
**(Java | Python | C++)**  

---

## 1️⃣ Problem Recap  

You’re given an integer array **`nums`**.  
You start with an array **`arr`** of the same length, all zeros.  
You may call one of two operations any number of times:

| Operation | Effect |
|---|---|
| `increment(i)` | Increase *arr[i]* by 1. |
| `double()` | Multiply **every** element in `arr` by 2. |

Your goal is to transform `arr` into `nums` with the **fewest** total operations.  
Return that minimum number of operations.

> **Constraints**  
> * 1 ≤ `nums.length` ≤ 10⁵  
> * 0 ≤ `nums[i]` ≤ 10⁹  
> * The answer always fits in a 32‑bit signed integer.

---

## 2️⃣ Key Insight – Think in Reverse  

If we *build* a number from 0 to its final value, every `1` bit forces us to use an `increment`.  
Every time we shift a binary representation left (i.e., multiply by 2), we use a `double`.  

**Reverse View**  
Start with the final numbers and peel them back to zero:

1. While a number is odd → we must have performed an `increment`.  
   (Remove that `1` by subtracting 1 → equivalent to `num & (num‑1)` in bits.)
2. Every time the number is even → a `double` was applied earlier.  
   (Divide by 2 → shift right.)

So:

* **Number of increments** = total pop‑count of all numbers.  
* **Number of doubles** = *maximum* number of right‑shifts needed for any element  
  = `(max bit‑length) – 1`.

The “–1” appears because the array starts at 0; the very first `double` is unnecessary if all numbers are zero.

---

## 3️⃣ Final Algorithm (Greedy & Bit‑Level)

```text
steps_inc  = 0
max_shift  = 0

for each x in nums:
    # Count 1‑bits (increments)
    steps_inc += popcount(x)

    # Track how many shifts (doubles) are required
    if x > 0:
        shifts = floor(log2(x))          # or bit_length - 1
        max_shift = max(max_shift, shifts)

return steps_inc + max_shift
```

*Time Complexity* – **O(n log M)**, where `M` is the maximum element.  
*Space Complexity* – **O(1)**.

Because `nums[i] ≤ 10⁹`, `log₂(M)` ≤ 30, so the algorithm is effectively linear in practice.

---

## 4️⃣ Reference Implementation  

### 4.1 Java  

```java
public class Solution {
    public int minOperations(int[] nums) {
        int addOps = 0;
        int maxShift = 0;

        for (int x : nums) {
            // Count 1‑bits
            addOps += Integer.bitCount(x);

            // Number of doubles needed for this element
            if (x > 0) {
                int shift = 31 - Integer.numberOfLeadingZeros(x); // floor(log2(x))
                maxShift = Math.max(maxShift, shift);
            }
        }
        return addOps + maxShift;
    }
}
```

> **Why `31 - numberOfLeadingZeros`?**  
> Java’s `Integer.bitCount` gives pop‑count.  
> `Integer.numberOfLeadingZeros` returns how many leading zero bits a 32‑bit int has;  
> subtracting from 31 yields the index of the highest set bit.

---

### 4.2 Python  

```python
def min_operations(nums):
    add_ops = 0
    max_shift = 0

    for x in nums:
        add_ops += bin(x).count('1')          # popcount
        if x:
            shift = x.bit_length() - 1        # floor(log2(x))
            max_shift = max(max_shift, shift)

    return add_ops + max_shift
```

*Note:* `int.bit_length()` returns the number of bits needed to represent the number in binary, i.e., `floor(log2(x)) + 1`.

---

### 4.3 C++  

```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        long long addOps = 0;          // use long long to avoid overflow during counting
        int maxShift = 0;

        for (int x : nums) {
            addOps += __builtin_popcount(x);   // popcount

            if (x) {
                int shift = 31 - __builtin_clz(x);   // floor(log2(x))
                maxShift = max(maxShift, shift);
            }
        }
        return static_cast<int>(addOps + maxShift);
    }
};
```

`__builtin_clz` counts leading zeros; `31 - clz(x)` is the highest set bit index.

---

## 5️⃣ Blog Article: “The Good, the Bad, and the Ugly of LeetCode 1558”

### 🎯 SEO‑Optimized Title  
**“LeetCode 1558 – Minimum Numbers of Function Calls to Make Target Array: A Bit‑Level Greedy Solution (Java, Python, C++)”**

### 🔎 Keywords  
* LeetCode 1558  
* Minimum number of function calls  
* Bit manipulation greedy  
* Java solution  
* Python solution  
* C++ solution  
* Coding interview problem  
* Software engineering interview  

---

### 📄 Article

#### 5.1 Introduction  
When preparing for a software‑engineering interview, the *“Minimum Numbers of Function Calls to Make Target Array”* problem often surfaces. It tests your ability to think *reverse* and leverage **bit manipulation** to achieve optimal time complexity. Below we dissect the problem, walk through an elegant greedy solution, and compare it against less efficient alternatives.

#### 5.2 Problem Restatement  
We start with an array of zeros and two operations: `increment(i)` (add 1 to a single element) and `double()` (multiply every element by 2). Our task is to transform the zero‑array into the target array `nums` with the **fewest** total operations.

#### 5.3 The “Good” – Greedy Bit‑Level Approach  
- **Simplicity**: Only two counters are needed – one for increments (`popcount` sum) and one for doubles (`max bit‑length – 1`).  
- **Time Efficiency**: O(n log M) where M ≤ 10⁹, which is essentially linear.  
- **Space Efficiency**: O(1).  
- **Intuitive Logic**: Every `1` bit demands an increment; each left shift demands a double.  
- **Language‑agnostic**: The same logic works in Java, Python, and C++ with minimal syntax differences.

#### 5.4 The “Bad” – Brute‑Force Simulation  
A naïve simulation would:

1. Iterate through each element, performing increments and doubles on the array itself until it matches `nums`.  
2. Each operation would modify the whole array, leading to O(n × #operations) time.  
3. This quickly explodes for `nums.length = 10⁵` and large numbers (up to 10⁹).  
4. Moreover, it uses O(n) extra space to store intermediate arrays.

**Why it’s a bad idea**: It doesn’t exploit the mathematical structure of the problem and leads to timeouts on real interview platforms.

#### 5.5 The “Ugly” – Over‑Optimized Math with Logs  
Some solutions try to pre‑compute the maximum number of doubles by repeatedly dividing the maximum number by 2:

```java
int maxShift = 0;
int x = *max(nums);
while (x > 0) { maxShift++; x /= 2; }
```

While correct, this approach:

- Uses integer division in a loop – still O(log M) but less readable.  
- Requires an extra pass to find the maximum.  
- Misses the more concise bit‑length calculation (`Integer.numberOfLeadingZeros`).  

The resulting code can be less maintainable and harder to understand at a glance.

#### 5.6 Final Thoughts & Interview Tips  
- **Explain the reverse perspective**: “We’re peeling back the binary digits.”  
- **Mention popcount**: Many interviewers love that you use built‑in bit tricks (`__builtin_popcount`, `Integer.bitCount`).  
- **State time and space complexity** upfront.  
- **Be ready to discuss edge cases**: All zeros, single element, large numbers.  
- **Show the code in your chosen language** – Java, Python, or C++ – and emphasize that the logic remains the same.

#### 5.7 Wrap‑Up  
The LeetCode 1558 problem exemplifies how a seemingly complex operation set collapses into a clean, linear solution when viewed through the lens of binary representation. Master this approach, and you’ll have a strong talking point for any algorithm interview.

---

### 🚀 Conclusion  

- **Java** – Use `Integer.bitCount` and `Integer.numberOfLeadingZeros`.  
- **Python** – Use `bin(x).count('1')` and `x.bit_length()`.  
- **C++** – Use `__builtin_popcount` and `__builtin_clz`.

All three solutions run in linear time and constant space, perfectly suited for the constraints of LeetCode 1558. Good luck in your next interview!