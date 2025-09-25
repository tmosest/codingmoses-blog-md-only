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

For every query we are on the one dimensional X‑axis

```
0 1 2 3 4 5 6 7 8 9 ...
```

`1 – x` – put an obstacle at position `x`.  
`2 – x – sz` – can we put a block of length `sz` whose rightmost point is
not larger than `x` ?

The block may start anywhere – even at `0` – but it must stay completely
inside the segment `[0 , x]` and it cannot cover an obstacle.



--------------------------------------------------------------------

#### 1.  Observations

For a position `p`

```
p is empty  →  a free segment of length 1
p has an obstacle  →  no free segment
```

If we know the longest consecutive empty segment inside any interval,
then a query `2 – x – sz` is answered by

```
maxLen( [0 , x] )  ≥  sz
```

because the longest empty segment inside `[0 , x]` is exactly the best
place for the block.

So we need a data structure that

* keeps the array of positions
* supports
    * **update** – “turn position `p` from empty to occupied”
    * **range query** – “maximum consecutive empty length in `[l , r]`”

The array size is at most `5·10⁴` (the usual limits of this problem),
therefore an ordinary segment tree with `O(log N)` updates and queries
is fully sufficient.



--------------------------------------------------------------------

#### 2.  Segment Tree structure

For every node we store

```
pre[node]  – length of the longest empty prefix of the node’s segment
suf[node]  – length of the longest empty suffix of the node’s segment
mx[node]   – length of the longest empty sub‑segment inside the node’s segment
len[node]  – length of the segment represented by the node
```

`len[node]` is kept only for convenience (it is computed once during
building).

Merging of two children `L` and `R`:

```
pre = (pre[L] == len[L]) ? len[L] + pre[R] : pre[L]
suf = (suf[R] == len[R]) ? len[R] + suf[L] : suf[R]
mx  = max( max(mx[L], mx[R]), suf[L] + pre[R] )
```

Initially every position is empty, therefore for a leaf  
`pre = suf = mx = 1`.

When an obstacle is placed at position `p` we change the leaf to
`0,0,0` (no empty cells) and propagate the changes upwards.



--------------------------------------------------------------------

#### 3.  Algorithm

```
find maximal x among all queries → N = maxX + 1  (positions 0 … maxX)
build segment tree on [0 , N-1]  (all values are 1)

for every query
    if type == 1 (place obstacle)
        update(position x)            // leaf becomes 0,0,0
    else (type == 2)
        mx = query(0 , x)
        answer = (mx >= sz)
```

All operations are `O(log N)`.



--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm returns `true` for a query `2 – x – sz`
iff a block of size `sz` can be placed with its rightmost point
not larger than `x`.

---

##### Lemma 1  
After every update the segment tree keeps correct values for all nodes.

**Proof.**

*Leaf:*  
When an obstacle is inserted at position `p`,
the leaf representing `p` is set to `pre = suf = mx = 0`.  
This is correct because at that single point there is no empty cell.

*Internal node:*  
Assume both children store correct information.
`pre` of the node is `len[childLeft]` if the left child’s prefix fills
its whole segment, otherwise it is simply `pre[childLeft]`.  
The same holds for `suf`.  
`mx` is the maximum of the two child `mx` values and of a segment that
spans the boundary: `suf[childLeft] + pre[childRight]`.  
All three values are exactly what the definition of a node requires.
∎



##### Lemma 2  
`query(0 , x)` returns the length of the longest consecutive empty
segment completely inside `[0 , x]`.

**Proof.**

`query` visits only nodes whose segments lie inside `[0 , x]` and
uses the `mx` value stored in those nodes (by Lemma&nbsp;1 each `mx`
is correct).  
When a node is fully inside the query interval it contributes its
`mx`.  
When a node is only partially inside, its two children are queried and
the maximum of the returned values is returned.  
Thus the maximum of all correct `mx` values inside `[0 , x]` is returned,
which is exactly the longest empty segment inside that interval. ∎



##### Lemma 3  
For a query `2 – x – sz` a block of length `sz` can be placed iff
`query(0 , x) ≥ sz`.

**Proof.**

*If part.*  
Assume the longest empty segment inside `[0 , x]` has length `L ≥ sz`.  
Let this segment be `[l , r]` with `r ≤ x`.  
Placing a block that starts at `l` and ends at `l+sz-1 ≤ r ≤ x` is
possible. So a placement exists.

*Only if part.*  
Assume a placement exists.  
The block occupies a contiguous segment `[b , b+sz-1]` with
`b+sz-1 ≤ x`.  
All positions of this segment are empty, therefore it is a sub‑segment
of an empty run inside `[0 , x]`.  
Consequently the longest empty segment inside `[0 , x]` has length at
least `sz`.  
Thus `query(0 , x) ≥ sz`. ∎



##### Theorem  
For every query of type `2 – x – sz` the algorithm outputs `true`
iff a block of size `sz` can be placed with its rightmost point not
larger than `x`.

**Proof.**

By Lemma&nbsp;2 the algorithm obtains `mx = query(0 , x)`, the length of
the longest empty segment inside `[0 , x]`.  
By Lemma&nbsp;3 a placement exists exactly when `mx ≥ sz`.  
The algorithm returns `mx ≥ sz`, therefore its answer is correct. ∎



--------------------------------------------------------------------

#### 5.  Complexity Analysis

Let `M` be the maximum position appearing in all queries  
(`M ≤ 5·10⁴` in the standard problem).

```
building the tree:   O(M)
each update:          O(log M)
each query:           O(log M)
```

The number of queries is at most `5·10⁴`, hence

```
Total time   :  O( (number_of_queries) · log M )  ≤  5·10⁴ · log 5·10⁴  <  10⁶
Memory usage :  O(4·(M+1)) integers  ≈  200 000 integers
```

Both are well inside the limits.



--------------------------------------------------------------------

#### 6.  Reference Implementation  (Java 17)

```java
import java.io.*;
import java.util.*;

public class Main {

    /* ------------  Segment Tree  ------------ */

    static class SegTree {
        int n;                       // number of positions (0 … n-1)
        int[] mx, pre, suf, len;     // node arrays, 1‑based node indices

        SegTree(int n) {
            this.n = n;
            int sz = 4 * n + 5;
            mx = new int[sz];
            pre = new int[sz];
            suf = new int[sz];
            len = new int[sz];
            build(1, 0, n - 1);
        }

        private void build(int node, int l, int r) {
            len[node] = r - l + 1;
            if (l == r) {                 // leaf – initially empty
                mx[node] = pre[node] = suf[node] = 1;
                return;
            }
            int mid = (l + r) >> 1;
            build(node << 1, l, mid);
            build(node << 1 | 1, mid + 1, r);
            pull(node);
        }

        private void pull(int node) {
            int left = node << 1, right = node << 1 | 1;
            int lenL = len[left], lenR = len[right];

            pre[node] = pre[left] == lenL ? lenL + pre[right] : pre[left];
            suf[node] = suf[right] == lenR ? lenR + suf[left] : suf[right];
            int cross = suf[left] + pre[right];
            mx[node] = Math.max(Math.max(mx[left], mx[right]), cross);
        }

        /* set position pos to obstacle (no empty cells) */
        void update(int pos) {
            update(1, 0, n - 1, pos);
        }

        private void update(int node, int l, int r, int pos) {
            if (l == r) {                 // leaf
                pre[node] = suf[node] = mx[node] = 0;
                return;
            }
            int mid = (l + r) >> 1;
            if (pos <= mid) update(node << 1, l, mid, pos);
            else            update(node << 1 | 1, mid + 1, r, pos);
            pull(node);
        }

        /* maximum consecutive empty length in [ql , qr] */
        int query(int ql, int qr) {
            return query(1, 0, n - 1, ql, qr);
        }

        private int query(int node, int l, int r, int ql, int qr) {
            if (ql <= l && r <= qr) return mx[node];
            int mid = (l + r) >> 1;
            int res = 0;
            if (ql <= mid) res = Math.max(res, query(node << 1, l, mid, ql, qr));
            if (qr > mid)  res = Math.max(res, query(node << 1 | 1, mid + 1, r, ql, qr));
            return res;
        }
    }

    /* ------------  Main  ------------ */

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st;

        int T = Integer.parseInt(br.readLine().trim());   // number of test cases
        StringBuilder out = new StringBuilder();

        while (T-- > 0) {
            st = new StringTokenizer(br.readLine());
            int Q = Integer.parseInt(st.nextToken());     // number of queries

            int maxX = 0;
            int[][] queries = new int[Q][3];   // store all queries

            for (int i = 0; i < Q; i++) {
                st = new StringTokenizer(br.readLine());
                int type = Integer.parseInt(st.nextToken());
                if (type == 1) {
                    int x = Integer.parseInt(st.nextToken());
                    queries[i][0] = 1;
                    queries[i][1] = x;
                    maxX = Math.max(maxX, x);
                } else { // type 2
                    int x = Integer.parseInt(st.nextToken());
                    int sz = Integer.parseInt(st.nextToken());
                    queries[i][0] = 2;
                    queries[i][1] = x;
                    queries[i][2] = sz;
                    maxX = Math.max(maxX, x);
                }
            }

            SegTree stree = new SegTree(maxX + 1);   // positions 0 … maxX

            for (int i = 0; i < Q; i++) {
                int type = queries[i][0];
                if (type == 1) {                     // put obstacle
                    int x = queries[i][1];
                    stree.update(x);
                } else {                            // query 2 – x – sz
                    int x = queries[i][1];
                    int sz = queries[i][2];
                    int longest = stree.query(0, x);
                    out.append(longest >= sz ? "true\n" : "false\n");
                }
            }
        }
        System.out.print(out.toString());
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to Java 17.  The `Main` class can be renamed or the code
wrapped into a method – the segment tree implementation is completely
independent.