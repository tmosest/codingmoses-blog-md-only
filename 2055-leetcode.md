---
title: LeetCode 2055. Plates Between Candles - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Plates Between Candles**

In every query `[x , y]` we need the first candle to the right of `x`  
and the first candle to the left of `y`.  
If those two candles exist, the answer is the number of `*` characters
between them (inclusive of neither candle).

--------------------------------------------------------------------

#### 1.  Pre‑processing

For a string `s` of length `n`

| array | description | how to compute |
|-------|-------------|----------------|
| `starPrefix` | `starPrefix[i]` = number of `*` before index `i` (exclusive) | walk left‑to‑right, keep a running count |
| `pipeLeft`   | index of the closest candle **to the left** of `i` (or `-1` if none) | walk left‑to‑right, remember the last seen candle |
| `pipeRight`  | index of the closest candle **to the right** of `i` (or `-1` if none) | walk right‑to‑left, remember the last seen candle |

All three arrays are built in **O(n)** time and **O(n)** space.

--------------------------------------------------------------------

#### 2.  Answering a query

```
x = queries[i][0]     // left endpoint of the query
y = queries[i][1]     // right endpoint

i1 = pipeRight[x]     // index of first candle >= x
i2 = pipeLeft[y]      // index of last candle <= y

if i1 == -1 or i2 == -1 or i1 >= i2          → answer = 0
else answer = starPrefix[i2] - starPrefix[i1]
```

`starPrefix[i2]` counts all plates up to `i2‑1`;  
`starPrefix[i1]` counts plates up to `i1‑1`;  
their difference is the number of plates strictly between the two candles.

--------------------------------------------------------------------

#### 3.  Complexity

*Time* : `O(n + q)` (one linear pass, then one constant‑time step per query)  
*Space*: `O(n)` (three auxiliary integer arrays)

--------------------------------------------------------------------

#### 4.  Java implementation

```java
import java.util.*;

class Solution {
    public int[] platesBetweenCandles(String s, int[][] queries) {
        int n = s.length();

        // 1. build auxiliary arrays
        int[] starPrefix = new int[n];      // plates up to i-1
        int[] pipeLeft  = new int[n];      // nearest candle index to the left
        int[] pipeRight = new int[n];      // nearest candle index to the right

        int starCnt = 0;
        int lastLeft = -1;   // -1 means no candle seen yet
        int lastRight = -1;

        // left‑to‑right pass
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (c == '*') {
                starCnt++;
                starPrefix[i] = starCnt;
            } else {            // '|'
                starPrefix[i] = starCnt;   // same number of plates so far
                lastLeft = i;              // store candle position
            }
            pipeLeft[i] = lastLeft;         // index of last candle to the left
        }

        // right‑to‑left pass
        for (int i = n - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (c == '*') {
                // nothing to do for stars
            } else {            // '|'
                lastRight = i;      // first candle to the right of current
            }
            pipeRight[i] = lastRight;       // index of first candle to the right
        }

        // 2. answer all queries
        int q = queries.length;
        int[] ans = new int[q];

        for (int k = 0; k < q; k++) {
            int L = queries[k][0];
            int R = queries[k][1];

            int leftCandle  = pipeRight[L];   // first candle >= L
            int rightCandle = pipeLeft[R];    // last candle <= R

            if (leftCandle == -1 || rightCandle == -1 || leftCandle >= rightCandle) {
                ans[k] = 0;
            } else {
                ans[k] = starPrefix[rightCandle] - starPrefix[leftCandle];
            }
        }
        return ans;
    }
}
```

The code follows the steps outlined above and runs in linear time with respect
to the input size.