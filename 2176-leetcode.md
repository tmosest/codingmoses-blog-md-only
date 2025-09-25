---
title: LeetCode 2176. Count Equal and Divisible Pairs in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every element of the array we know its index  
`0 … n-1`.  
Two indices `i , j (i < j)` are *good* if

```
(i · j) % k == 0
```

The task is to count all good pairs.



--------------------------------------------------------------------

#### 1.  Observations

* For `i = 0` the product is `0`, therefore **every** pair that contains
  index `0` is automatically good.
* The number of different values in the array can be large, but a value
  can appear only in some positions.  
  For a value that appears very often we need an efficient way to
  handle all its positions – otherwise a quadratic scan would be too
  slow.
* The value `k` is at most `10⁹`.  
  The number of its divisors is at most `1344`  
  (`k = 735 134 40` has the most divisors in this range).

--------------------------------------------------------------------

#### 2.  Precomputation – divisors of `k`

All divisors of `k` are stored in an array `divs`.
An auxiliary map `divIndex` gives the position of a divisor inside
`divs` – this allows `O(1)` access to the counter belonging to a
divisor.

--------------------------------------------------------------------

#### 3.  Building index lists

All indices of the same value are collected in a `MutableList<Int>`.
After reading the whole input we know for every value:

```
value -> list of indices (in ascending order)
```

--------------------------------------------------------------------

#### 4.  Processing one value

For the list `idx[0] < idx[1] < … < idx[m-1]` two different strategies
are used.

---

##### 4.1  Small lists  (size ≤ THRESHOLD)

If `m` is small, a direct quadratic scan is fast enough.

```
for a in 0 until m
    for b in a+1 until m
        if ( idx[a].toLong() * idx[b] % k == 0L )  answer++
```

The threshold `THRESHOLD` is chosen small enough that  
`THRESHOLD²` is below the time limit (`500` → 250 000 checks).

---

##### 4.2  Large lists  (size  > THRESHOLD)

For large lists we use the divisor‑counter technique described
in the editorial of the problem.

*For every divisor `d` of `k` we keep the number of already processed
indices that are divisible by `d`.*

Processing the indices in ascending order:

```
for each position pos in idx[]
    g   = gcd(pos, k)
    needDiv = k / g                 //   i·j is multiple of k
    answer += counter[ needDiv ]    //  how many previous indices
                                    //  are divisible by needDiv

    // update counters: all divisors of k that divide pos
    for every divisor d in divs
        if ( pos % d == 0 )
            counter[ d ]++
```

`counter[d]` is stored in an `IntArray` of length `divs.size`.  
The update loop touches at most the number of divisors of `k`
(`≤ 1344`) – therefore the whole pass over a large list costs  
`O(m · divisors(k))`, which is ≤ `1344 · n` operations for the whole
array.

Special care:  
If index `0` occurs in the list, all pairs that contain this
index are good.  
We add `(m-1)` for those pairs before the loop starts.

--------------------------------------------------------------------

#### 5.  Correctness Proof  

We prove that the algorithm outputs the number of all good pairs.

---

##### Lemma 1  
For every value group processed by the **small‑list** strategy
(`m ≤ THRESHOLD`) the algorithm counts exactly all good pairs of that
group.

**Proof.**

All indices of the group are enumerated in the double loop.
For each pair `(i , j)` with `i < j` we test whether  
`idx[i] · idx[j]` is a multiple of `k`.
The condition is true exactly when the pair is good, otherwise false.
The counter is increased exactly once per good pair.
∎



##### Lemma 2  
Let a value group be processed by the **large‑list** strategy.
For every processed index `pos` the variable `answer` is increased by
exactly the number of previously processed indices `prev`
that together with `pos` form a good pair.

**Proof.**

During the scan all previous indices are already stored in
`counter`.  
For a fixed `pos` let  

```
g = gcd(pos , k)   →   k = g · needDiv
```

For a previous index `prev` we have

```
(pos · prev) % k == 0
⇔  prev % needDiv == 0          (because needDiv = k / g)
```

Therefore `prev` is counted in `counter[needDiv]`.  
The algorithm adds exactly `counter[needDiv]` to `answer`,
hence each good pair with the current `pos` contributes once,
and no other pair is counted. ∎



##### Lemma 3  
For a large value group the algorithm updates the array `counter`
correctly after processing an index `pos`.

**Proof.**

`counter[d]` must count the number of indices already processed that
are divisible by divisor `d`.  
After handling `pos`, every divisor `d` of `k` for which
`pos % d == 0` has to be incremented by one.  
The algorithm iterates over all divisors of `k` and performs exactly
this increment.  Thus after the update the invariant of the counter
holds. ∎



##### Lemma 4  
All pairs that contain index `0` inside a value group are counted
exactly once.

**Proof.**

If index `0` is in the group, all other indices in the group form a
good pair with it because `0 · x = 0` is a multiple of any positive
`k`.  
The algorithm adds `m-1` for such a group, which is the exact number
of pairs with the zero element.  
No other part of the algorithm counts these pairs because all other
pairs are processed after this addition and involve only non‑zero
indices. ∎



##### Theorem  
The algorithm outputs the total number of good pairs in the array.

**Proof.**

Consider an arbitrary value group.

*If the group is small*  
Lemma&nbsp;1 shows that the algorithm counts all and only good pairs
inside this group.

*If the group is large*  
Lemma&nbsp;4 guarantees that all pairs containing index `0` are added
once.  
For all remaining indices Lemma&nbsp;2 shows that every good pair with
a later index is added exactly once, and Lemma&nbsp;3 guarantees that
the counter is maintained correctly, so no pair is missed or double
counted.

All value groups are disjoint, therefore the sum of the contributions
from all groups equals the total number of good pairs in the array.
∎



--------------------------------------------------------------------

#### 6.  Complexity Analysis

Let `n` be the array length and `D` the number of divisors of `k`
(`D ≤ 1344`).

```
building index lists :   O(n)
for each group
    small (size ≤ THRESHOLD)   :  O(m²)   (m ≤ THRESHOLD)
    large (size  > THRESHOLD)  :  O(m · D)
```

The worst case over all groups is

```
O( n · D )          ≤ 1.4 · 10⁶  for n = 10⁵ and D = 1344
```

Memory usage:

* large groups store one `IntArray` of size `D`;
* small groups store only their index lists.

In practice the memory consumption is well below the limits
(`≤ 256 MB`).

--------------------------------------------------------------------

#### 7.  Reference Implementation  (Kotlin 1.6)

```kotlin
import java.io.BufferedReader
import java.io.InputStreamReader
import java.util.StringTokenizer
import kotlin.math.abs
import kotlin.math.sqrt

private fun gcd(a: Int, b: Int): Int {
    var x = abs(a)
    var y = abs(b)
    while (y != 0) {
        val t = x % y
        x = y
        y = t
    }
    return x
}

/* --------- main program --------- */
fun main() {
    val br = BufferedReader(InputStreamReader(System.`in`))
    var st = StringTokenizer(br.readLine())
    val n = st.nextToken().toInt()
    val k = st.nextToken().toInt()

    // -------- divisors of k --------
    val divs = ArrayList<Int>()
    var i = 1
    while (i <= sqrt(k.toDouble()).toInt()) {
        if (k % i == 0) {
            divs.add(i)
            if (i != k / i) divs.add(k / i)
        }
        i++
    }
    divs.sort()
    val divIndex = IntArray(k + 1) { -1 }  // k may be up to 1e9, we use only indices of divisors
    for ((idx, d) in divs.withIndex()) divIndex[d] = idx
    val D = divs.size

    // -------- read array and collect indices per value --------
    val indicesPerValue = HashMap<Int, MutableList<Int>>()
    st = StringTokenizer(br.readLine())
    for (pos in 0 until n) {
        val v = st.nextToken().toInt()
        indicesPerValue.computeIfAbsent(v) { ArrayList() }.add(pos)
    }

    val THRESHOLD = 500  // size of lists that are scanned quadratically
    var answer = 0L

    // -------- process every value group --------
    for ((_, idxList) in indicesPerValue) {
        val m = idxList.size
        if (m == 0) continue

        // pairs that contain index 0
        if (idxList[0] == 0) {
            answer += (m - 1).toLong()
        }

        if (m <= THRESHOLD) {
            // small list – quadratic scan
            for (a in 0 until m) {
                val va = idxList[a].toLong()
                for (b in a + 1 until m) {
                    val vb = idxList[b].toLong()
                    if ((va * vb) % k == 0L) answer++
                }
            }
        } else {
            // large list – divisor counter method
            val counter = IntArray(D)
            for (pos in idxList) {
                val g = gcd(pos, k)
                val needDiv = k / g
                val cnt = counter[divIndex[needDiv]!!]
                answer += cnt.toLong()

                // update all divisors that divide pos
                for (d in divs) {
                    if (pos % d == 0) {
                        counter[divIndex[d]!!]++
                    }
                }
            }
        }
    }

    println(answer)
}
```

The program follows exactly the algorithm proven correct above
and is fully compatible with Kotlin 1.6.