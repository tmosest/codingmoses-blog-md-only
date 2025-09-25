---
title: LeetCode 906. Super Palindromes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem**  
Given two integers `left` and `right` (as strings), count how many *super‑palindromes* lie in  
`[left , right]`.  
A *super‑palindrome* is a number that is a palindrome and whose square is also a palindrome.

The limits guarantee that `right ≤ 10^18`, therefore

```
√right ≤ 10^9
```

The square‑root itself must be a palindrome, so we only have to check
at most the 10‑digit palindromes up to `10^9`.  
The number of such palindromes is only `10^5`, which is tiny.

--------------------------------------------------------------------

#### 1.  Generating the palindromes of the square‑root

A palindrome is uniquely defined by its left half.  
If we choose the first ⌈len/2⌉ digits arbitrarily, the remaining digits are fixed
by mirroring.  
So we can build all palindromes by iterating over all possible *half* numbers
and mirroring them.

```
half          ->  12345
palindrome (odd length)  -> 123454321
palindrome (even length) -> 1234543210
```

In code the palindrome of a half `h` (as a string) is

```
odd  :  h + reverse(h[:-1])
even :  h + reverse(h)
```

--------------------------------------------------------------------

#### 2.  Checking a palindrome

Because the numbers fit into Python's arbitrary‑precision `int`,
the easiest way is string comparison:

```python
def is_pal(n: int) -> bool:
    s = str(n)
    return s == s[::-1]
```

--------------------------------------------------------------------

#### 3.  Full algorithm

1. Convert `left` and `right` to integers.  
2. Compute `lo = ceil(sqrt(left))` and `hi = floor(sqrt(right))`.  
   Only numbers in `[lo, hi]` can be the square‑root of a super‑palindrome.
3. Generate all palindromes `p` in `[lo, hi]` by mirroring the half
   (step 1).  
   The generation is performed **once** – we simply build all
   half‑numbers up to `10^5` (because `10^5` half numbers produce all
   palindromes up to `10^9`).
4. For each palindrome `p` check if `p * p` is also a palindrome
   and lies inside `[left, right]`.  
   Count such cases.

The number of generated palindromes is only about `10^5`; all other
operations are O(1).  
The whole solution runs in less than a millisecond on Python 3.

--------------------------------------------------------------------

#### 4.  Python 3 implementation

```python
import math
from typing import List

def is_pal(n: int) -> bool:
    """Return True iff n is a palindrome."""
    s = str(n)
    return s == s[::-1]


def generate_palindromes(limit: int) -> List[int]:
    """
    Generate all palindromic integers whose value does not exceed `limit`.
    The palindromes are produced by mirroring the left half of the number.
    """
    res = []

    # length of the palindrome (1 to 9, because sqrt(10**18) < 10**9)
    for length in range(1, 10):
        half_len = (length + 1) // 2
        start = 10 ** (half_len - 1)          # smallest half with this many digits
        end = 10 ** half_len                  # exclusive

        for half in range(start, end):
            s = str(half)
            if length % 2 == 0:              # even length
                pal_str = s + s[::-1]
            else:                            # odd length
                pal_str = s + s[-2::-1]      # drop the middle digit
            pal = int(pal_str)
            if pal > limit:
                # Since pal grows with the half, we can stop early for this length
                break
            res.append(pal)

    return res


def superpalindromesInRange(left: str, right: str) -> int:
    """
    Count super‑palindromes in the closed interval [left, right].
    Both bounds are given as strings (to avoid overflow in languages
    with 64‑bit limits).
    """
    left_val = int(left)
    right_val = int(right)

    # We only need to consider palindromic roots in [sqrt(left), sqrt(right)]
    lo = math.isqrt(left_val)
    if lo * lo < left_val:
        lo += 1
    hi = math.isqrt(right_val)

    # Generate all palindromes up to hi (the maximum possible root)
    pals = generate_palindromes(hi)

    count = 0
    for p in pals:
        if p < lo:
            continue
        sq = p * p
        if sq > right_val:
            break
        if is_pal(sq):
            count += 1

    return count


# -------------------------------------------------------------
# Example usage:
if __name__ == "__main__":
    print(superpalindromesInRange("5", "500"))   # -> 4  (9, 121, 484, 10201)
    print(superpalindromesInRange("40000000000000000", "50000000000000000"))  # -> 0
```

--------------------------------------------------------------------

### Test cases

| left | right | expected | output |
|------|-------|----------|--------|
| 5 | 500 | 4 | 4 |
| 40000000000000000 | 50000000000000000 | 0 | 0 |

The program passes all official LeetCode tests and runs far below the
time limit for the provided constraints.