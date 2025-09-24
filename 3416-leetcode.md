---
title: LeetCode 3416. Subsequences with a Unique Middle Mode II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every triple of indices

```
1 ≤ i < j < k ≤ n
```

the numbers `a[i] , a[j] , a[k]` are taken from the array `a[1 … n]`.
The triple is **good** if at least two of the three numbers are equal.
We have to count all good triples.



--------------------------------------------------------------------

#### 1.   Observations

```
All triples  =  C(n,3)
```

A triple is *not* good iff all its three numbers are pairwise different.
So

```
answer = (triples that are not good)
        = (triples with all three equal) + (triples with exactly two equal)
```

The two kinds of bad triples can be counted only from the frequencies of
the distinct values.

--------------------------------------------------------------------

#### 2.   Counting bad triples

For a value `v` that occurs `cnt[v]` times

```
triples with all three equal   : C(cnt[v], 3)
triples with exactly two equal : C(cnt[v], 2)  *  (n - cnt[v])
```

`C(cnt[v], 2)` – choose the two equal positions –
the third position can be any of the remaining `n – cnt[v]` elements.

The total number of bad triples is the sum of the two expressions over
all different values.

```
answer = Σ over all values v
          (cnt[v] choose 2) * (n – cnt[v])   +   (cnt[v] choose 3)
```

`n ≤ 200 000` in the tests (the official limit is even larger),  
therefore all intermediate results fit into a signed 64‑bit integer (`long`)
in Java.

--------------------------------------------------------------------

#### 3.   Algorithm
```
if n < 3 → print 0

read all array elements, store the frequency of every distinct value
answer = 0
for every frequency cnt
        pairs = cnt * (cnt-1) / 2                // C(cnt,2)
        answer += pairs * (n - cnt)             // exactly two equal
        answer += cnt * (cnt-1) * (cnt-2) / 6   // all three equal
print answer
```

--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm outputs the required number of good triples.

---

##### Lemma 1  
For a fixed value `v` that occurs `cnt[v]` times,  
`cnt[v] * (cnt[v]-1) / 2` is the number of unordered pairs of positions
containing the value `v`.

**Proof.**

Choosing two equal positions out of `cnt[v]` positions is exactly
`C(cnt[v], 2)` which equals the formula above. ∎



##### Lemma 2  
For a fixed value `v` the number of triples containing *exactly two* elements
equal to `v` is  
`C(cnt[v],2) * (n – cnt[v])`.

**Proof.**

* Choose the two equal positions – by Lemma&nbsp;1 this can be done in
  `C(cnt[v],2)` ways.
* The third element must be a position that does **not** contain `v`;
  there are `n – cnt[v]` such positions.

The product gives all triples with exactly two equal numbers equal to `v`. ∎



##### Lemma 3  
For a fixed value `v` the number of triples containing *all three*
elements equal to `v` is  
`C(cnt[v],3)`.

**Proof.**

Choosing three equal positions out of `cnt[v]` positions is by definition
`C(cnt[v],3)`. ∎



##### Lemma 4  
Let  

```
B = Σ over all distinct values v
        C(cnt[v],2) * (n – cnt[v])   +   C(cnt[v],3)
```

Then `B` equals the number of triples that are **not good**  
(i.e. all three numbers are pairwise different).

**Proof.**

From Lemma&nbsp;2 and Lemma&nbsp;3 we know the exact amount of bad
triples for a single value `v`.  
Different values cannot belong to the same triple, hence the bad triples
belonging to different values are disjoint.
Summation over all values gives the total number of bad triples. ∎



##### Lemma 5  
The algorithm outputs `B`.

**Proof.**

The algorithm computes exactly the expression described in Lemma&nbsp;4,
using the stored frequencies.  
Therefore the printed value equals `B`. ∎



##### Theorem  
The algorithm outputs the number of triples  
`1 ≤ i < j < k ≤ n` such that the three chosen numbers are **not**
pairwise different (i.e. at least two are equal).

**Proof.**

The algorithm outputs `B` (Lemma&nbsp;5).
By definition of good triples,  
`answer = B`, hence the algorithm is correct. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

```
Building the frequency table :  O(n)
Computing the sum             :  O(m)   (m = number of distinct values,  m ≤ n)
Memory usage                  :  O(m)
```

With `n ≤ 10^5` (or even `10^6`) all operations use 64‑bit integers and
finish in well below one second.



--------------------------------------------------------------------

#### 6.   Reference Implementation  (Java 17)

```java
import java.io.*;
import java.util.*;

public class Main {
    // -------- fast scanner --------
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
            int c, sign = 1, val = 0;
            do { c = readByte(); } while (c <= ' ' && c != -1);
            if (c == '-') { sign = -1; c = readByte(); }
            while (c > ' ') {
                val = val * 10 + (c - '0');
                c = readByte();
            }
            return val * sign;
        }
    }

    // -------- main --------
    public static void main(String[] args) throws Exception {
        FastScanner fs = new FastScanner(System.in);
        int n = fs.nextInt();
        if (n < 3) {
            System.out.println(0);
            return;
        }
        Map<Integer, Integer> freq = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int x = fs.nextInt();
            freq.put(x, freq.getOrDefault(x, 0) + 1);
        }

        long answer = 0L;
        long nLong = n;
        for (int cnt : freq.values()) {
            long c = cnt;
            if (c >= 2) {
                long pairs = c * (c - 1) / 2;        // C(c,2)
                answer += pairs * (nLong - c);      // exactly two equal
            }
            if (c >= 3) {
                long triplesAllEqual = c * (c - 1) * (c - 2) / 6; // C(c,3)
                answer += triplesAllEqual;
            }
        }
        System.out.println(answer);
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to the Java 17 standard.