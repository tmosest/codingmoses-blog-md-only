---
title: LeetCode 3161. Block Placement Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For the staircase we know the limits

```
1 , 2 , … , MAX
```

During the game some of those positions become *obstacles*.
If you are standing on a step `a` you may walk only to the right.
As soon as you would step on an obstacle the walk stops.
The query `P a` asks for the maximal number of steps that can be taken
starting from step `a`.

--------------------------------------------------------------------

#### 1.   Observation

An obstacle never disappears – it only gets added.
All obstacles together divide the whole range into disjoint
intervals

```
(-∞ , firstObstacle] ,
(firstObstacle , secondObstacle] ,
(secondObstacle , thirdObstacle] , … ,
(lastObstacle , +∞)
```

The walk starting at `a` can use only the interval that contains `a`
and it must stop **before** the next obstacle on the right.
Therefore the answer for a query `P a` is

```
nextObstacleOnRight – a – 1
```

`nextObstacleOnRight` is the first obstacle whose position is larger
than `a`.  
If `a` itself is an obstacle the walk is already blocked – the answer
is `0`.

--------------------------------------------------------------------

#### 2.   Data structure

The set of obstacle positions must support

* insert a new obstacle,
* ask whether a position is an obstacle,
* find the first obstacle larger than a given position.

All operations are needed in logarithmic time.
A balanced binary search tree (`TreeSet` in Java) is perfect for that.
Two fictitious obstacles are inserted once per test case:

```
-1        – just before the first real step
MAX + 1   – just after the last real step
```

With those two sentinels `higher(a)` (the first element larger than
`a`) is always defined for every `a` between `1` and `MAX`.

--------------------------------------------------------------------

#### 3.   Algorithm

For every test case

```
read MAX and Q
obs ← TreeSet containing -1 and MAX+1

repeat Q times
        read type (C or P) and value v
        if type = 'C'          // create an obstacle
                obs.add(v)
        else                    // type = 'P', query
                if obs.contains(v)
                        output 0
                else
                        next ← obs.higher(v)
                        output (next - v - 1)
```

--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm prints the correct answer for every query.

---

##### Lemma 1  
Let `O` be the first obstacle strictly larger than `a`.  
Any walk that starts at step `a` and does not step on an obstacle
contains exactly `O - a - 1` steps.

**Proof.**

All steps between `a` and `O-1` are free of obstacles, because
`O` is the first obstacle on the right.  
The walk can move to `O-1`, which is the last free step.
After that the next step would be on `O`, an obstacle, which is
not allowed.  
Thus the walk consists of the steps  

```
a , a+1 , … , O-1
```

whose length is `O-1 - a + 1 = O - a - 1`. ∎



##### Lemma 2  
If step `a` itself is an obstacle, the maximum number of steps that
can be taken from `a` equals `0`.

**Proof.**

You start on an obstacle, therefore you are already blocked and
cannot make any step. ∎



##### Lemma 3  
For every step `a` between `1` and `MAX` the algorithm returns  
`0` if `a` is an obstacle and otherwise returns `O - a - 1`,
where `O` is the first obstacle on the right of `a`.

**Proof.**

The set `obs` always contains all current obstacles.
If `a` is in `obs` the algorithm outputs `0` – by Lemma&nbsp;2
this is the correct answer.

Otherwise the algorithm obtains `next = obs.higher(a)`.
Because the set also contains the sentinels `-1` and `MAX+1`,
`next` is precisely the first obstacle strictly larger than `a`.
The algorithm outputs `next - a - 1`.  
By Lemma&nbsp;1 this equals the length of the longest admissible walk
starting from `a`. ∎



##### Theorem  
For every query of type `P a` the algorithm prints the maximum
possible number of steps that can be walked from step `a`.

**Proof.**

By Lemma&nbsp;3 the value printed by the algorithm equals the length
of the longest walk that does not cross an obstacle.
Any admissible walk must end before the first obstacle on the right,
hence no longer walk exists.
Therefore the printed value is exactly the required maximum. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

Let `N` be the number of obstacles that have been added
(including the two sentinels).

*Each insertion* or *lookup* in `TreeSet` costs `O(log N)` time.  
All `Q` queries are processed in  
`O(Q · log N)` time.  
The set stores at most `N` values, so the memory consumption is
`O(N)`.

--------------------------------------------------------------------

#### 6.   Reference Implementation (Java 17)

```java
import java.io.*;
import java.util.*;

public class Main {

    /* ---------- fast scanner ---------- */
    private static class FastScanner {
        private final BufferedInputStream in;
        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;

        FastScanner(InputStream is) { in = new BufferedInputStream(is); }

        private int readByte() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0) return -1;
            }
            return buffer[ptr++];
        }

        private String next() throws IOException {
            StringBuilder sb = new StringBuilder();
            int c;
            do {
                c = readByte();
                if (c == -1) return null;
            } while (Character.isWhitespace(c));

            do {
                sb.append((char) c);
                c = readByte();
            } while (c != -1 && !Character.isWhitespace(c));

            return sb.toString();
        }

        long nextLong() throws IOException {
            String s = next();
            if (s == null) throw new IOException("EOF");
            return Long.parseLong(s);
        }
    }
    /* ---------------------------------- */

    public static void main(String[] args) throws Exception {
        FastScanner fs = new FastScanner(System.in);
        StringBuilder out = new StringBuilder();

        String token;
        while ((token = fs.next()) != null) {          // read MAX
            long MAX = Long.parseLong(token);
            long Q = fs.nextLong();                    // number of queries

            TreeSet<Long> obs = new TreeSet<>();
            obs.add(-1L);                 // sentinel before the staircase
            obs.add(MAX + 1);             // sentinel after the staircase

            for (long i = 0; i < Q; i++) {
                String type = fs.next();  // "C" or "P"
                long v = fs.nextLong();

                if (type.charAt(0) == 'C') {          // add obstacle
                    obs.add(v);
                } else {                              // query
                    if (obs.contains(v)) {
                        out.append('0').append('\n');
                    } else {
                        long next = obs.higher(v);     // first obstacle > v
                        out.append(next - v - 1).append('\n');
                    }
                }
            }
        }

        System.out.print(out.toString());
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to the Java 17 standard.