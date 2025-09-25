---
title: LeetCode 2217. Find Palindrome With Fixed Length - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **K‑th Palindrome – a math‑based solution**

For a fixed length `L` we do **not** have to enumerate all `L`‑digit numbers.
A palindrome is completely determined by its *left half* – the right half is just
the reverse of it.  
So we only have to consider the numbers

```
 10^(ceil(L/2)-1) , 10^(ceil(L/2)-1)+1 , … , 10^(ceil(L/2))-1
```

(`ceil(L/2)` is the number of digits of the left half).

--------------------------------------------------------------------

### 1.  How many palindromes of length L exist?

```
count(L) = 9 * 10^(ceil(L/2)-1)
```

`10^(ceil(L/2)-1)` is the smallest left half (`1 0 … 0`), the leading digit
cannot be `0`.  The factor `9` comes from the possible leading digits `1…9`.

--------------------------------------------------------------------

### 2.  Construct the k‑th palindrome

For a given `k ( 1‑based )`:

```
half   = (k-1) + 10^(ceil(L/2)-1)        # left half as an integer
left   = str(half)
right  = left[:len(left)-L%2]            # drop the middle digit if L is odd
pal    = left + right[::-1]              # concatenate & reverse
```

The whole expression is returned as a 64‑bit integer (`long` in Java,
`int`/`long` in Python).

--------------------------------------------------------------------

### 3.  Code (Java)

```java
class Solution {
    private long getPalindrome(long k, int halfLen, int L) {
        long leftNum = (k - 1) + (long)Math.pow(10, halfLen - 1); // left part
        String left  = Long.toString(leftNum);
        String right = left.substring(0, left.length() - (L % 2)); // drop mid
        return Long.parseLong(left + new StringBuilder(right).reverse());
    }

    public long[] kthPalindrome(int[] queries, int L) {
        int n = queries.length;
        long[] ans = new long[n];

        int halfLen = (L + 1) / 2;                      // ceil(L/2)
        long maxCnt  = 9L * (long)Math.pow(10, halfLen - 1);  // 9 * 10^(halfLen-1)

        for (int i = 0; i < n; ++i) {
            long k = queries[i];
            if (k > maxCnt) {
                ans[i] = -1;
            } else {
                ans[i] = getPalindrome(k, halfLen, L);
            }
        }
        return ans;
    }
}
```

--------------------------------------------------------------------

### 4.  Code (Python)

```python
class Solution:
    def kthPalindrome(self, queries: List[int], intLength: int) -> List[int]:
        half = (intLength + 1) // 2
        base = 10 ** (half - 1)            # smallest left half
        max_cnt = 9 * 10 ** (half - 1)

        ans = []
        for k in queries:
            if k > max_cnt:
                ans.append(-1)
            else:
                left = str(base + k - 1)
                right = left[::-1]
                if intLength % 2 == 1:
                    right = right[1:]       # drop the middle digit
                ans.append(int(left + right))
        return ans
```

--------------------------------------------------------------------

### 5.  Why this works

* The number of palindromes of length `L` is at most `9 * 10^(ceil(L/2)-1)`, which fits
  comfortably into a 64‑bit integer for the constraints of the problem.
* The `k`‑th left half is simply the `k`‑th number in the range
  `[10^(ceil(L/2)-1) , 10^(ceil(L/2))-1]`.
* By mirroring this half we get the palindrome immediately – no enumeration,
  no recursion, no huge memory usage.

--------------------------------------------------------------------

**Result**

The algorithm runs in `O(|queries|)` time and `O(1)` auxiliary space,
solving the problem efficiently even for the maximal limits.