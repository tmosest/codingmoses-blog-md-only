---
title: LeetCode 3337. Total Characters in String After Transformations II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Goal**

After `t` transformations the string can be astronomically long (up to 25ⁿ).  
We only need its length – that is the total number of characters present after all
transformations.  
The key observation is that the *exact arrangement* of the characters is irrelevant,
only how many of each of the 26 letters exist.  
If we can compute the final frequency of every letter, summing them gives the
answer.

--------------------------------------------------------------------

### 1.  Count the letters in the starting string

```
cnt[i]   = number of occurrences of the i‑th letter (a → 0, … , z → 25)
```

This needs `O(|s|)` time and is the only place where the original string is
inspected.

--------------------------------------------------------------------

### 2.  How does one letter change?

For a letter `c` (0…25) we are given `nums[c] = k`.  
During one transformation `c` is replaced by the next `k` letters of the
alphabet (wrapping around).  
So in a single step:

```
c → (c+1) mod 26
c → (c+2) mod 26
        …
c → (c+k) mod 26
```

--------------------------------------------------------------------

### 3.  Transition matrix

We build a 26 × 26 matrix **T** where

```
T[i][j] = number of letters j produced from a single letter i in one step
```

For every `i` (letter) and every `x = 1 … nums[i]`

```
next = (i + x) mod 26
T[i][next] += 1
```

The matrix already contains the effect of the wrap‑around, and each entry
is 0 or 1 (because each produced letter is unique in one step).

--------------------------------------------------------------------

### 4.  Many steps – fast exponentiation

After `t` steps a single letter `i` produces a distribution given by the
`i`‑th row of **T⁽ᵗ⁾** (the matrix T raised to power `t`).  
The whole transformation of the whole string is

```
final_counts = cnt · T⁽ᵗ⁾
```

(`cnt` is treated as a row vector).  

To compute `T⁽ᵗ⁾` we use binary exponentiation:

```
result = I          // identity matrix
power  = t
while power > 0
    if power is odd  result = result · T
    T = T · T
    power = power / 2
```

The matrix size is only 26, so a multiplication takes  
`26³ ≈ 17 576` scalar operations – trivial for a computer.  
We perform it `O(log t)` times, which is at most 30 multiplications for
`t ≤ 10⁹`.

All arithmetic is performed modulo `MOD = 1 000 000 007` to avoid overflow.

--------------------------------------------------------------------

### 5.  Apply the powered matrix to the initial frequencies

```
for i in 0…25
    for j in 0…25
        final_counts[j] += cnt[i] * T⁽ᵗ⁾[i][j]   (mod MOD)
```

Only `26²` operations are needed.

--------------------------------------------------------------------

### 6.  Result

The answer is the total number of letters after all transformations:

```
answer = sum(final_counts) mod MOD
```

--------------------------------------------------------------------

### 7.  Complexity

| Step                | Time                                  | Memory  |
|---------------------|----------------------------------------|---------|
| Counting `cnt`      | `O(|s|)`                               | `O(26)` |
| Building **T**     | `O(26²)`                                | `O(26²)`|
| Matrix exponentiation| `O(26³ log t)` (≈ 1.7 k × log t) | `O(26²)`|
| Vector × matrix     | `O(26²)`                                | `O(26)` |
| Final sum           | `O(26)`                                 | –       |

The dominant term is the matrix exponentiation; everything else is negligible.

--------------------------------------------------------------------

### 8.  Why this works

- **Linearity** – The number of each letter after one step is a linear
  combination of the numbers before the step; matrices encode exactly that
  linear transformation.
- **Power of a matrix** – Repeating the same linear step `t` times is the
  same as applying the matrix `t` times, i.e. multiplying by `T⁽ᵗ⁾`.  
  Fast exponentiation computes `T⁽ᵗ⁾` in logarithmic time.
- **Modulo** – The counts grow exponentially; using the modulus keeps all
  intermediate results bounded.
- **No string construction** – We never build the gigantic string; we only
  keep 26 counters.

--------------------------------------------------------------------

**Result:**  
By counting the initial letters, building the transition matrix, raising it to
the power `t`, applying it to the initial counts, and summing the resulting
frequencies, we obtain the length of the string after `t` transformations,
modulo `1 000 000 007`.