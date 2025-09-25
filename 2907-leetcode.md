---
title: LeetCode 2907. Maximum Profitable Triplets With Increasing Prices I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every index `j` we would like to know

* the largest profit that can be earned by buying at an index `i < j`
  whose price is **smaller** than `prices[j]`  (this will be the first
  buy‑sell pair),

* the largest profit that can be earned by selling at an index `k > j`
  whose price is **greater** than `prices[j]`  (this will be the second
  buy‑sell pair).

If both values are known, the third profit is simply `profits[j]` –
the profit that comes from the middle transaction.  
The best answer is

```
max( prefixMax[j] + profits[j] + suffixMax[j] )   over all j
```

where

```
prefixMax[j] = max{ profits[i] | i < j  and  prices[i] < prices[j] }
suffixMax[j] = max{ profits[k] | k > j  and  prices[k] > prices[j] }
```

--------------------------------------------------------------------

#### 1.  Coordinate compression

`prices` can be as large as 10⁹, but we only need to know the relative
order of the values.  
All distinct prices are sorted, duplicates removed and each price is
replaced by its position in that sorted list (`1 … m`).  
After compression the order “price value is smaller” is the same as
“compressed index is smaller”.

--------------------------------------------------------------------

#### 2.  Prefix maximum – a Binary Indexed Tree (Fenwick)

We scan the arrays from left to right.  
For the current index `j`

```
idx      = compressed value of prices[j]                (1 … m)
bestPrev = query(bitLeft , idx-1)   // max profit with compressed index < idx
prefixMax[j] = bestPrev
update(bitLeft , idx , profits[j])   // current profit is now available
```

`bitLeft` is a Fenwick tree that keeps for every prefix the maximum
profit seen so far.  
The tree is initialised with a very small number (`INF_NEG`).

--------------------------------------------------------------------

#### 3.  Suffix maximum – another Binary Indexed Tree

We scan the arrays from right to left.  
The trick is to use a *reversed* index:

```
revIdx   = m - idx + 1
bestSuf  = query(bitRight , revIdx-1)   // max profit with original index > idx
suffixMax[j] = bestSuf
update(bitRight , revIdx , profits[j])
```

Because the reversed indices decrease when the original price increases,
`query(... , revIdx-1)` gives exactly the maximum among all prices
strictly larger than `prices[j]`.

--------------------------------------------------------------------

#### 4.  Final answer

```
answer = INF_NEG
for every j
    if prefixMax[j] > INF_NEG and suffixMax[j] > INF_NEG
        answer = max(answer , prefixMax[j] + profits[j] + suffixMax[j])

return (answer==INF_NEG ? 0 : answer)
```

If no triple of indices satisfies the condition, the maximum possible
sum is `0` (the statement does not forbid this situation).

--------------------------------------------------------------------

#### 5.  Correctness Proof  

We prove that the algorithm returns the maximum possible sum of the
three profits.

---

##### Lemma 1  
For every index `j`, after the left‑to‑right scan `prefixMax[j]` equals  
`max{ profits[i] | i < j  and  prices[i] < prices[j] }`.

**Proof.**

The Fenwick tree `bitLeft` is updated only with elements that already
appear on the left of the current index `j`.  
When the scan reaches `j`, the tree contains exactly the profits of all
indices `i < j`.  
`query(bitLeft , idx-1)` returns the maximum profit among those
indices whose compressed price is strictly smaller than the current
price, i.e. `prices[i] < prices[j]`.  
Thus the stored value equals the required maximum. ∎



##### Lemma 2  
For every index `j`, after the right‑to‑left scan `suffixMax[j]` equals  
`max{ profits[k] | k > j  and  prices[k] > prices[j] }`.

**Proof.**

During the right‑to‑left scan the Fenwick tree `bitRight` holds the
profits of indices strictly to the right of the current position `j`.  
Because we use the reversed index `revIdx = m - idx + 1`, all indices
with an original compressed value larger than `idx` correspond to
indices with a *smaller* reversed value.  
Therefore `query(bitRight , revIdx-1)` returns the maximum profit among
exactly those indices `k > j` with `prices[k] > prices[j]`. ∎



##### Lemma 3  
For every index `j` for which both `prefixMax[j]` and `suffixMax[j]`
are defined (not `INF_NEG`),  
`prefixMax[j] + profits[j] + suffixMax[j]` is the maximum possible sum
of the three profits of a valid triple whose middle index is `j`.

**Proof.**

The three required indices must satisfy

```
i < j < k
prices[i] < prices[j] > prices[k]
```

* By Lemma&nbsp;1, `prefixMax[j]` is the maximum possible profit that
  can be earned by choosing `i` (the first buy‑sell pair).

* `profits[j]` is fixed for the middle transaction.

* By Lemma&nbsp;2, `suffixMax[j]` is the maximum possible profit that
  can be earned by choosing `k` (the second buy‑sell pair).

Thus the sum of these three profits is the best achievable sum for all
triples whose middle index is exactly `j`. ∎



##### Lemma 4  
Let `best` be the maximum of  
`prefixMax[j] + profits[j] + suffixMax[j]` over all indices `j`.  
Then `best` equals the maximum sum of profits over **all** valid triples.

**Proof.**

Any valid triple has a unique middle index `j`.  
By Lemma&nbsp;3, for that `j` the algorithm considers the optimal
combination that uses this middle index, which is at least as good as
the given triple.  
Taking the maximum over all `j` therefore yields a value that is at
least as large as the optimum and, obviously, cannot be larger than
the optimum.  
Hence it equals the optimum. ∎



##### Theorem  
The algorithm returns the maximum possible sum of the three profits
obeying the required order of transactions.

**Proof.**

The algorithm computes `best` as described in Lemma&nbsp;4 and
returns it (or `0` if no triple exists).  
By Lemma&nbsp;4, `best` is exactly the optimal value. ∎



--------------------------------------------------------------------

#### 6.  Complexity Analysis

Let `n = prices.length`, `m` = number of distinct prices.

*Coordinate compression* : `O(n log n)` time, `O(n)` memory  
*Prefix scan*              : `O(n log m)` time  
*Suffix scan*              : `O(n log m)` time  
*Final maximum*            : `O(n)` time  

Overall

```
Time   :  O(n log n)
Memory :  O(n)
```

--------------------------------------------------------------------

#### 7.  Reference Implementation  (Java 17)

```java
import java.io.*;
import java.util.*;

public class Main {

    // very small sentinel value – smaller than any possible profit
    private static final int INF_NEG = -2_000_000_000;

    /* ---------- Fenwick tree for prefix maximum ---------- */
    private static void update(int[] bit, int idx, int val) {
        while (idx < bit.length) {
            if (val > bit[idx]) bit[idx] = val;
            idx += idx & -idx;
        }
    }

    private static int query(int[] bit, int idx) {
        int res = INF_NEG;
        while (idx > 0) {
            if (bit[idx] > res) res = bit[idx];
            idx -= idx & -idx;
        }
        return res;
    }

    /* ---------- Main solving routine ---------- */
    public static int maximumProfit(int[] prices, int[] profits) {
        int n = prices.length;
        if (n < 3) return 0;                     // not enough indices

        /* 1. coordinate compression of prices */
        int[] sorted = prices.clone();
        Arrays.sort(sorted);
        List<Integer> uniq = new ArrayList<>();
        for (int v : sorted) {
            if (uniq.isEmpty() || uniq.get(uniq.size() - 1) != v)
                uniq.add(v);
        }
        int m = uniq.size();                     // number of compressed values
        int[] comp = new int[n];
        Map<Integer, Integer> mp = new HashMap<>();
        for (int i = 0; i < m; ++i) mp.put(uniq.get(i), i + 1);
        for (int i = 0; i < n; ++i) comp[i] = mp.get(prices[i]);

        /* 2. Prefix scan – largest profit to the left with smaller price */
        int[] bitLeft = new int[m + 2];
        Arrays.fill(bitLeft, INF_NEG);
        int[] prefixMax = new int[n];
        for (int j = 0; j < n; ++j) {
            int idx = comp[j];
            int bestPrev = query(bitLeft, idx - 1);
            prefixMax[j] = bestPrev;
            update(bitLeft, idx, profits[j]);
        }

        /* 3. Suffix scan – largest profit to the right with larger price */
        int[] bitRight = new int[m + 2];
        Arrays.fill(bitRight, INF_NEG);
        int[] suffixMax = new int[n];
        for (int j = n - 1; j >= 0; --j) {
            int idx = comp[j];
            int revIdx = m - idx + 1;
            int bestSuf = query(bitRight, revIdx - 1);
            suffixMax[j] = bestSuf;
            update(bitRight, revIdx, profits[j]);
        }

        /* 4. Best possible sum */
        int answer = INF_NEG;
        for (int j = 0; j < n; ++j) {
            if (prefixMax[j] > INF_NEG && suffixMax[j] > INF_NEG) {
                int cur = prefixMax[j] + profits[j] + suffixMax[j];
                if (cur > answer) answer = cur;
            }
        }
        return answer == INF_NEG ? 0 : answer;
    }

    /* ---------- I/O helpers (not required by the judge) ---------- */
    public static void main(String[] args) throws IOException {
        FastScanner fs = new FastScanner(System.in);
        int n = fs.nextInt();
        int[] prices = new int[n];
        int[] profits = new int[n];
        for (int i = 0; i < n; ++i) prices[i] = fs.nextInt();
        for (int i = 0; i < n; ++i) profits[i] = fs.nextInt();

        System.out.println(maximumProfit(prices, profits));
    }

    /* ---------- fast scanner for integers ---------- */
    private static class FastScanner {
        private final InputStream in;
        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;

        FastScanner(InputStream in) { this.in = in; }

        private int readByte() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0) return -1;
            }
            return buffer[ptr++];
        }

        int nextInt() throws IOException {
            int c, sgn = 1, val = 0;
            do c = readByte(); while (c <= ' ' && c != -1);
            if (c == '-') { sgn = -1; c = readByte(); }
            while (c > ' ') {
                val = val * 10 + (c - '0');
                c = readByte();
            }
            return val * sgn;
        }
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to the Java 17 standard.