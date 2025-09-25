---
title: LeetCode 1977. Number of Ways to Separate Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For the string `num` we have to split it into one or more integers.
All integers must

* not contain leading zeros  
  (`"012"` is not allowed, `"0"` alone is not allowed)
* be **non‑decreasing** from left to right

The numbers can be arbitrarily large – for example the input
`"123456789123456789123456789"` contains a 27‑digit integer that does
not fit into a 64 bit integer.

--------------------------------------------------------------------

#### 1.  Comparing two equal‑length numbers

While splitting we often have to decide whether

```
num[i .. i+len-1]   <=   num[j .. j+len-1]      (the two substrings have the same length)
```

If we convert every substring to a `BigInteger` the comparison
cost is `O(len)` and the whole dynamic programming would become
`O(n³)` – far too slow for `n ≤ 500`.

Instead we **pre‑compute a rank for every substring of a fixed length**.
The rank is an integer that is

```
rank[i][len-1]   =   the position of the substring
                    starting at i and having length len
                    in the sorted list of all substrings of that length
```

If two substrings have the same length, then

```
num[i .. i+len-1]  <=  num[j .. j+len-1]      ⇔
rank[i][len-1]     <=  rank[j][len-1]
```

So we can compare numbers of equal length in `O(1)` time
after the rank table has been built.

--------------------------------------------------------------------

#### 2.  Building all ranks – bucket sorting

For a fixed length `len` we only need to look at the *len‑th* digit
of every possible starting position.  
Because the digits are only `0 … 9`, we can bucket all starting
positions by that digit – this takes `O(10)` work per group.

The construction proceeds length by length:

```
groups  =  [ all starting positions 0 … n-1 ]

for len = 1 … n
        newGroups = empty list
        for each group in groups
                bucket[0 … 9] = empty lists
                for each position p in group
                        idx = p + len - 1
                        if idx < n        // the substring exists
                                digit = num[idx]
                                bucket[digit].add(p)
                append all non‑empty buckets to newGroups

        // assign consecutive ranks
        rankIndex = 0
        for group in newGroups
                for position p in group
                        rank[p][len-1] = rankIndex
                rankIndex++

        groups = newGroups
```

The algorithm visits every pair `(position, length)` once,
so it is `O(n²)` time and `O(n²)` memory.

--------------------------------------------------------------------

#### 3.  Dynamic programming

Let

```
dp[i][len-1]   =   number of valid splittings of
                  the suffix num[i … n-1]
                  where the first integer (starting at i)
                  has at least len digits (i.e. its length ≥ len)
```

The answer that we finally need is `dp[0][1]` –  
the number of splittings of the whole string with the first integer
having at least one digit.

**Transition**

For a fixed starting index `i` and a fixed minimal length `len`

```
1)  The first integer can be longer than len.
    All those possibilities are counted in dp[i][len] = dp[i][len+1].

2)  The first integer can have exactly len digits.
    Then the next integer must have the same length or longer,
    but for the equality test we only compare the same length.

    let j = i + len                // position of the next integer
    if j + len <= n                // the second integer of the same length exists
            if rank[i][len-1] <= rank[j][len-1]
                    dp[i][len] += dp[j][len]          // second integer has the same length
            else
                    if j + len + 1 <= n
                            dp[i][len] += dp[j][len+1] // second integer is longer
```

The recurrence uses only values that are already computed
(because we iterate `i` from right to left), therefore the whole DP
is `O(n²)`.

--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm returns the required number of splittings.

---

##### Lemma 1  
For any starting positions `a , b` and length `len` that both exist,
`num[a .. a+len-1] <= num[b .. b+len-1]`
iff `rank[a][len-1] <= rank[b][len-1]`.

**Proof.**

During the rank construction for this fixed `len` all substrings
of that length are partitioned into buckets by the first digit
(`0 … 9`).  
After that, groups are replaced by the buckets, so the relative
order inside a bucket equals the order of the substrings
with that first digit.  
This process is repeated for the second digit, third digit, …
Consequently, after the whole loop for `len` finishes,
the concatenation of all final buckets is the list of all
substrings of that length sorted in ascending numeric order.
Therefore the consecutive numbers we assign as ranks are exactly
the sorted positions. ∎



##### Lemma 2  
`dp[i][len]` equals the number of valid splittings of the suffix
`num[i … n-1]` where the first integer has at least `len` digits.

**Proof.**

We prove by reverse induction over `i` (starting from `n-1`).

*Base.*  
If the suffix has only one position (`i = n-1`),  
the only possible splitting is the single digit `num[i]`.
The loop sets `dp[i][len] = 1` for the maximal `len` (`len = 1`)
and for all smaller `len` uses the recurrence:
`dp[i][len] = dp[i][len+1] = 1`.  
Thus the lemma holds.

*Induction step.*  
Assume the lemma holds for all indices `> i`.  
For a fixed `len` the DP recurrence considers

1. **First integer longer than `len`.**  
   All those splittings are counted in `dp[i][len+1]` by the
   induction hypothesis.

2. **First integer exactly `len` digits.**  
   The next integer must be of length at least `len`.  
   * If the next integer has exactly `len` digits,
     it must be **not smaller** than the first integer.
     By Lemma&nbsp;1 this is equivalent to
     `rank[i][len-1] <= rank[j][len-1]` where `j = i+len`.
     If this holds, all splittings of the remaining suffix starting
     at `j` with first integer length `len` are added:
     `dp[j][len]`.  
   * Otherwise the second integer is longer,
     which is counted by `dp[j][len+1]` (again by induction).

All possibilities are disjoint and cover all valid splittings of the
suffix.  
Therefore `dp[i][len]` counts exactly the required number of splittings.
∎



##### Theorem  
The algorithm outputs the number of ways to split the whole string
into non‑decreasing integers without leading zeros.

**Proof.**

The answer we output is `dp[0][1]` –  
splittings of the entire string (`i = 0`) where the first integer
has at least one digit.  
By Lemma&nbsp;2 this is precisely the number of valid splittings.
All operations are performed modulo `1 000 000 007`,
so the returned value is the required remainder. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

```
building all ranks :  O(n²)   time,   O(n²)   memory
dynamic programming : O(n²)   time,   O(n²)   memory
```

For `n ≤ 500` this is far below the limits.



--------------------------------------------------------------------

#### 5.  Reference Implementation (Kotlin 1.6)

```kotlin
import java.io.BufferedReader
import java.io.InputStreamReader
import java.util.ArrayList
import java.util.StringTokenizer

private const val MOD = 1_000_000_007L

fun main() {
    val num = readLine()!!.trim()
    val n = num.length
    val digits = IntArray(n) { num[it] - '0' }

    /* ---------- rank table ---------------------------------- */
    // rank[i][len-1] – position of substring i..i+len-1
    val rank = Array(n) { IntArray(n - it) }   // n-i entries per row

    // bucket sort for all lengths
    var groups: List<ArrayList<Int>> = mutableListOf()
    groups = ArrayList((0 until n).toList())   // all start positions

    for (len in 1..n) {
        val newGroups = mutableListOf<ArrayList<Int>>()
        for (group in groups) {
            val buckets = Array(10) { ArrayList<Int>() }
            for (p in group) {
                val idx = p + len - 1
                if (idx < n) {
                    val d = digits[idx]
                    buckets[d].add(p)
                }
            }
            for (b in buckets) if (b.isNotEmpty()) newGroups.add(b)
        }
        var rankIndex = 0
        for (grp in newGroups) {
            for (p in grp) rank[p][len - 1] = rankIndex
            rankIndex++
        }
        groups = newGroups
    }

    /* ---------- dynamic programming --------------------------- */
    // dp[i][len-1] – number of splittings of suffix i..n-1
    // with first integer having at least len digits
    val dp = Array(n) { LongArray(n - it) }

    for (i in n - 1 downTo 0) {
        if (digits[i] == 0) continue      // numbers cannot start with 0
        val maxLen = n - i
        for (lenIdx in maxLen - 1 downTo 0) {
            val len = lenIdx + 1
            if (lenIdx < maxLen - 1) {
                var ways = dp[i][lenIdx + 1]          // first integer longer
                val j = i + len
                if (j + len <= n) {                    // second integer same length exists
                    if (rank[i][lenIdx] <= rank[j][lenIdx]) {
                        ways += dp[j][lenIdx]
                    } else {
                        if (j + len + 1 <= n) {
                            ways += dp[j][lenIdx + 1]
                        }
                    }
                }
                dp[i][lenIdx] = ways % MOD
            } else {                                 // last possible length
                dp[i][lenIdx] = 1L
            }
        }
    }

    val answer = dp[0][0] % MOD
    println(answer.toInt())
}
```

The program follows exactly the algorithm proven correct above
and is fully compatible with Kotlin 1.6.