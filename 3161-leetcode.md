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

For every distance `x ( 0 ≤ x ≤ 50 000 )` we can build an obstacle at that
integer distance from the origin.
The obstacles are built one after another – all commands have to be processed
in the given order.

```
type 1  x   →  build an obstacle at distance x
type 2  sz  x  →  is there a block of length sz (consecutive integers)
                    that covers only distances ≤ x ?
```

For every command we have to output

```
true  –  the command can be executed
false –  the command cannot be executed
```

The task is a classical *dynamic set of points on a line* problem.



--------------------------------------------------------------------

#### 1.   Observations

*All distances are integers.*  
If an obstacle exists at distance `d`, no part of a block may contain `d`.

For a block of length `sz` to exist inside the interval `[0 , x]`
the block has to end somewhere `≤ x`.  
Let

```
prev(x) – the closest obstacle that is ≤ x
```

(there is always an obstacle at distance `0`; we will keep it in the set)
All distances `prev(x)+1 … x` are obstacle‑free,
therefore a block can end at `x` **iff**

```
x – prev(x)  ≥  sz            (the free stretch on the right side)
```

Otherwise the block must end before `x`.  
The longest stretch that ends before `x` is the maximum length of an
interval that ends at any obstacle `≤ prev(x)`.  
If that maximum length is at least `sz` the block can be placed,
otherwise it cannot.

So we need

```
for a given obstacle position x:
    1. find its predecessor and successor in the current obstacle set
    2. store for every obstacle the length of the free stretch to its left
```

--------------------------------------------------------------------

#### 2.   Data structures

* `TreeSet<Integer> obstacles` – all built obstacles, sorted.
  We insert two sentinels:
  * `0`  – the origin is always free at the beginning
  * `50 001` – a dummy obstacle that lies beyond the maximum possible query
    distance.  
    It guarantees that every obstacle has a successor.

* `SegmentTree` over the indices `0 … 50 001`.  
  For every obstacle `p` the tree stores

  ```
  leftGap[p] = p – predecessor_of(p)
  ```

  This is the length of the free interval that ends exactly at `p`.

  The segment tree supports

  * point update – set `leftGap[p]` to a new value
  * range maximum query – maximum value on an interval of indices

  Both operations work in `O(log N)` time (`N = 50 002`).

--------------------------------------------------------------------

#### 3.   Algorithm

```
initialise:
    obstacles = { 0 , 50001 }
    segTree   = new SegmentTree(50002)
    segTree.update(0,       0)        // leftGap[0]  = 0
    segTree.update(50001, 50001)      // leftGap[50001] = 50001

for every command:
    if command is type 1  x:
        prev  = obstacles.lower(x)     // closest obstacle ≤ x
        next  = obstacles.higher(x)    // closest obstacle  > x
        obstacles.add(x)
        segTree.update(x,        x - prev)     // leftGap for new obstacle
        segTree.update(next,     next - x)     // free part after new obstacle
        answer = true

    else (type 2 sz x):
        prev   = obstacles.lower(x)  // closest obstacle ≤ x
        leftGap = x - prev
        if leftGap >= sz:
            answer = true
        else:
            maxGap = segTree.queryMax(0, prev)   // longest free stretch that ends before or at prev
            answer = (maxGap >= sz)

    store answer in output list
```

--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm outputs the correct answer for every command.

---

##### Lemma 1  
For any obstacle position `p` the value stored in the segment tree
at index `p` equals the length of the free interval that ends exactly at `p`,
i.e. `leftGap[p] = p – predecessor_of(p)`.

**Proof.**

*Initialization.*  
`obstacles = {0, 50001}`.  
`leftGap[0]` is set to `0` – the free interval that ends at `0` has length `0`.  
`leftGap[50001]` is set to `50001 – 0 = 50001` – the free interval `[0,50001)`.

*Induction step.*  
Assume the statement holds after all previous commands.
When a new obstacle `x` is inserted:

```
prev = obstacles.lower(x)   // predecessor of x
next = obstacles.higher(x)  // successor of x
```

`x` becomes a new obstacle.  
The free interval that ends at `x` has length `x – prev`; we store this value
at index `x`.  
The free interval that ends at the old successor `next`
now has length `next – x`; we update this value at index `next`.  
All other indices are unchanged.  
Therefore the invariant holds after this command. ∎



##### Lemma 2  
For a query `type 2 sz x` let `p = obstacles.lower(x)`  
(`p` is the closest obstacle `≤ x`).  
`leftGap = x – p`.

If `leftGap ≥ sz` then a block of length `sz` can be placed covering only
distances `≤ x`.

**Proof.**

All distances between `p+1` and `x` are free, because `p` is the
nearest obstacle to the left of `x`.  
If `x` itself is not an obstacle (`x` ≠ `p`), the stretch
`[p+1 , x]` has length `leftGap`.  
Placing a block that ends at distance `x` is therefore possible
iff `leftGap ≥ sz`. ∎



##### Lemma 3  
For a query `type 2 sz x` let `p = obstacles.lower(x)`  
and `maxGap = segTree.queryMax(0, p)`  
(the maximum free stretch that ends at any obstacle `≤ p`).

If `maxGap ≥ sz` then a block of length `sz` can be placed covering only
distances `≤ x`.

**Proof.**

All free stretches that end before or at `p` are exactly the intervals
`[prev(q)+1 , q]` for obstacles `q ≤ p`.  
By Lemma&nbsp;1 the segment tree stores their lengths at the indices `q`.  
The maximum of these lengths is `maxGap`.  
If `maxGap ≥ sz`, choose the corresponding interval `[a , b]`
(`b ≤ p < x`) and place the block inside it; all its covered distances
are `< b ≤ x`. ∎



##### Lemma 4  
If both conditions of Lemma&nbsp;2 and Lemma&nbsp;3 fail,
no block of length `sz` can be placed covering only distances `≤ x`.

**Proof.**

Assume the contrary: a block of length `sz` exists.

*Case 1 – the block ends at distance `x`.*  
Then all distances from the left end of the block up to `x`
are free.  
By definition of `p = obstacles.lower(x)` the leftmost obstacle in that
range is `p`.  
Hence `x – p ≥ sz`.  
But this contradicts the assumption that the first condition failed.

*Case 2 – the block ends before `x`.*  
Let it end at some distance `y < x`.  
`y` is not an obstacle, therefore the block is completely inside the
interval that ends at some obstacle `q ≤ y`.  
The length of that interval is `q – predecessor_of(q)`  
(and stored in the segment tree at index `q`).
Since `q ≤ y < x`, `q ≤ p`.  
Thus the length of that interval is at most `maxGap`.  
The block’s length `sz` would therefore be `≤ maxGap`.  
But the second condition failed, so `maxGap < sz`.  
Contradiction again. ∎



##### Theorem  
For every command the algorithm outputs

* `true`   iff the command can be executed,
* `false`  otherwise.

**Proof.**

*Type&nbsp;1.*  
The algorithm only inserts the new obstacle, no answer is required.
This obviously satisfies the specification.

*Type&nbsp;2.*  
Let `p = obstacles.lower(x)`.  
The algorithm first checks `x – p ≥ sz`.  
By Lemma&nbsp;2 this is equivalent to the existence of a suitable block
ending at `x`.  
If that fails, it queries the maximum free stretch that ends at an obstacle
`≤ p`.  
By Lemma&nbsp;3 the existence of a block that ends before `x` is equivalent
to this maximum being at least `sz`.  
If both checks fail, Lemma&nbsp;4 proves that no block can exist.
Thus the answer returned by the algorithm is correct. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

Let `N = 50 002` – number of indices in the segment tree (`0 … 50 001`).

* Each `type 1` command  
  * `TreeSet.lower` / `higher` – `O(log N)`  
  * two point updates in the segment tree – `O(log N)`  
  * insertion into the set – `O(log N)`  

  → **`O(log N)`**

* Each `type 2` command  
  * `TreeSet.lower` – `O(log N)`  
  * one range maximum query on the segment tree – `O(log N)`  

  → **`O(log N)`**

Memory consumption

* `TreeSet` – at most `N` integers – `O(N)`  
* Segment tree – 4 × `N` integers (classic implementation) – `O(N)`  

Total memory: **`O(N)`** (≈ 0.5 MB).

--------------------------------------------------------------------

#### 6.   Reference Implementation  (Java 17)

```java
import java.io.*;
import java.util.*;

/** Dynamic obstacles and range maximum query via segment tree. */
public class Main {

    /* -----------  Segment Tree for range maximum  ------------- */
    private static class SegmentTree {
        private final int n;          // size of the tree (next power of two)
        private final int[] tree;     // max values

        SegmentTree(int size) {
            n = 1;
            while (n < size) n <<= 1;
            tree = new int[2 * n];
        }

        // set value at position idx to val
        void update(int idx, int val) {
            int p = idx + n;
            tree[p] = val;
            while (p > 1) {
                p >>= 1;
                tree[p] = Math.max(tree[p << 1], tree[(p << 1) | 1]);
            }
        }

        // maximum value on indices [l, r] inclusive
        int queryMax(int l, int r) {
            if (l > r) return Integer.MIN_VALUE;
            l += n; r += n;
            int res = Integer.MIN_VALUE;
            while (l <= r) {
                if ((l & 1) == 1) res = Math.max(res, tree[l++]);
                if ((r & 1) == 0) res = Math.max(res, tree[r--]);
                l >>= 1; r >>= 1;
            }
            return res;
        }
    }

    /* --------------------  Main  ---------------------------- */
    public static void main(String[] args) throws IOException {
        FastScanner fs = new FastScanner(System.in);
        int m = fs.nextInt();                  // number of commands

        final int MAX = 50001;                 // sentinel beyond queries
        TreeSet<Integer> obstacles = new TreeSet<>();
        obstacles.add(0);
        obstacles.add(MAX + 1);                // 50001

        SegmentTree seg = new SegmentTree(MAX + 2); // indices 0 … 50001
        seg.update(0, 0);
        seg.update(MAX + 1, MAX + 1);          // leftGap[50001] = 50001

        StringBuilder out = new StringBuilder();

        for (int i = 0; i < m; i++) {
            int type = fs.nextInt();
            if (type == 1) {                    // add obstacle
                int x = fs.nextInt();
                Integer prev = obstacles.lower(x);
                Integer next = obstacles.higher(x);
                obstacles.add(x);
                seg.update(x, x - prev);
                seg.update(next, next - x);
                out.append("true\n");
            } else {                            // query
                int sz = fs.nextInt();
                int x  = fs.nextInt();
                Integer p = obstacles.lower(x);   // nearest obstacle ≤ x
                int leftGap = x - p;
                boolean ans;
                if (leftGap >= sz) {
                    ans = true;
                } else {
                    int maxGap = seg.queryMax(0, p);
                    ans = (maxGap >= sz);
                }
                out.append(ans ? "true\n" : "false\n");
            }
        }
        System.out.print(out.toString());
    }

    /* ----------  Fast scanner for integers  ---------- */
    private static class FastScanner {
        private final InputStream in;
        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;
        FastScanner(InputStream in) { this.in = in; }

        private int read() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0) return -1;
            }
            return buffer[ptr++];
        }

        int nextInt() throws IOException {
            int c, sgn = 1, res = 0;
            do { c = read(); } while (c <= ' ');
            if (c == '-') { sgn = -1; c = read(); }
            while (c > ' ') {
                res = res * 10 + c - '0';
                c = read();
            }
            return res * sgn;
        }
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to the Java 17 language standard.